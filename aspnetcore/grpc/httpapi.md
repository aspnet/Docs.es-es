---
title: Creación de API web JSON desde gRPC
author: jamesnk
description: Obtenga información sobre cómo crear API HTTP JSON para servicios gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 78247a9b775ec8c9a3bc4a58209f09e5b714c07d
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217523"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="6afa9-103">Creación de API web JSON desde gRPC</span><span class="sxs-lookup"><span data-stu-id="6afa9-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="6afa9-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6afa9-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6afa9-105">La API HTTP de gRPC es un proyecto experimental, no un producto confirmado.</span><span class="sxs-lookup"><span data-stu-id="6afa9-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="6afa9-106">Queremos:</span><span class="sxs-lookup"><span data-stu-id="6afa9-106">We want to:</span></span>
>
> * <span data-ttu-id="6afa9-107">Comprobar que nuestro enfoque para crear API web JSON para servicios gRPC funciona.</span><span class="sxs-lookup"><span data-stu-id="6afa9-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="6afa9-108">Recibir comentarios sobre si este enfoque es útil para los desarrolladores de .NET.</span><span class="sxs-lookup"><span data-stu-id="6afa9-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="6afa9-109">[Deje sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167) para asegurarnos de que creamos algo que gusta a los desarrolladores y los hace productivos.</span><span class="sxs-lookup"><span data-stu-id="6afa9-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="6afa9-110">gRPC es una forma moderna de comunicarse entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6afa9-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="6afa9-111">gRPC usa HTTP/2, streaming, Protobuf y contratos de mensaje para crear servicios en tiempo real de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="6afa9-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="6afa9-112">Una limitación de gRPC es que no se puede usar en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="6afa9-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="6afa9-113">Los exploradores no son totalmente compatibles con HTTP/2, por lo que REST y JSON son la manera principal de obtener datos en aplicaciones de explorador.</span><span class="sxs-lookup"><span data-stu-id="6afa9-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="6afa9-114">Incluso con las ventajas que aporta gRPC, REST y JSON ocupan un lugar importante en las aplicaciones modernas.</span><span class="sxs-lookup"><span data-stu-id="6afa9-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="6afa9-115">La compilación de API web JSON \***y** _ gRPC agrega una sobrecarga no deseada al desarrollo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6afa9-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="6afa9-116">En este documento se explica cómo crear API web JSON con servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="6afa9-117">API HTTP de gRPC</span><span class="sxs-lookup"><span data-stu-id="6afa9-117">gRPC HTTP API</span></span>

<span data-ttu-id="6afa9-118">La API HTTP de gRPC es una extensión experimental para ASP.NET Core que crea API JSON de RESTful para servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="6afa9-119">Una vez configurada, la API HTTP de gRPC permite que las aplicaciones llamen a servicios gRPC con conceptos conocidos de HTTP:</span><span class="sxs-lookup"><span data-stu-id="6afa9-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="6afa9-120">_ Verbos HTTP</span><span class="sxs-lookup"><span data-stu-id="6afa9-120">_ HTTP verbs</span></span>
* <span data-ttu-id="6afa9-121">Enlace de parámetros de dirección URL</span><span class="sxs-lookup"><span data-stu-id="6afa9-121">URL parameter binding</span></span>
* <span data-ttu-id="6afa9-122">Solicitudes y respuestas JSON</span><span class="sxs-lookup"><span data-stu-id="6afa9-122">JSON requests/responses</span></span>

<span data-ttu-id="6afa9-123">gRPC también se puede usar para llamar a servicios.</span><span class="sxs-lookup"><span data-stu-id="6afa9-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="6afa9-124">Uso</span><span class="sxs-lookup"><span data-stu-id="6afa9-124">Usage</span></span>

1. <span data-ttu-id="6afa9-125">Agregue una referencia de paquete a [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="6afa9-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="6afa9-126">Registre los servicios en *Startup.cs* con `AddGrpcHttpApi`.</span><span class="sxs-lookup"><span data-stu-id="6afa9-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="6afa9-127">Agregue los archivos [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) y [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="6afa9-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="6afa9-128">Anote los métodos gRPC en los archivos *.proto* con enlaces y rutas HTTP:</span><span class="sxs-lookup"><span data-stu-id="6afa9-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

[!code-protobuf[](~/grpc/httpapi/greet.proto?highlight=3,9-11)]

<span data-ttu-id="6afa9-129">El método gRPC `SayHello` ya se puede invocar como gRPC+protobuf y como una API HTTP:</span><span class="sxs-lookup"><span data-stu-id="6afa9-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="6afa9-130">Solicitud: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="6afa9-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="6afa9-131">Respuesta: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="6afa9-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="6afa9-132">Los registros de servidor muestran que un servicio gRPC ejecuta la llamada HTTP.</span><span class="sxs-lookup"><span data-stu-id="6afa9-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="6afa9-133">La API HTTP de gRPC asigna la solicitud HTTP entrante a un mensaje de gRPC y, luego, convierte el mensaje de respuesta en JSON.</span><span class="sxs-lookup"><span data-stu-id="6afa9-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

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

<span data-ttu-id="6afa9-134">Este es un ejemplo básico.</span><span class="sxs-lookup"><span data-stu-id="6afa9-134">This is a basic example.</span></span> <span data-ttu-id="6afa9-135">Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para conocer más opciones de personalización.</span><span class="sxs-lookup"><span data-stu-id="6afa9-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="6afa9-136">Habilitación de la compatibilidad con Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="6afa9-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="6afa9-137">Swagger (OpenAPI) es una especificación independiente del lenguaje que sirve para describir API REST.</span><span class="sxs-lookup"><span data-stu-id="6afa9-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="6afa9-138">La API HTTP de gRPC puede integrarse con [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) para generar un punto de conexión de Swagger para servicios gRPC RESTful.</span><span class="sxs-lookup"><span data-stu-id="6afa9-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="6afa9-139">Por lo tanto, el punto de conexión de Swagger se puede usar con la [interfaz de usuario de Swagger](https://swagger.io/swagger-ui/) y otras herramientas.</span><span class="sxs-lookup"><span data-stu-id="6afa9-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="6afa9-140">Para habilitar Swagger con la API HTTP de gRPC:</span><span class="sxs-lookup"><span data-stu-id="6afa9-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="6afa9-141">Agregue una referencia de paquete a [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span><span class="sxs-lookup"><span data-stu-id="6afa9-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="6afa9-142">Configure Swashbuckle en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6afa9-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="6afa9-143">El método `AddGrpcSwagger` configura Swashbuckle para incluir los puntos de conexión de la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="6afa9-144">Para confirmar que Swashbuckle genera Swagger para los servicios gRPC RESTful, inicie la aplicación y vaya a la página de la interfaz de usuario de Swagger:</span><span class="sxs-lookup"><span data-stu-id="6afa9-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![Interfaz de usuario de Swagger](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="6afa9-146">API HTTP de gRPC frente a gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="6afa9-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="6afa9-147">Tanto la API HTTP de gRPC como gRPC-Web permiten llamar a servicios gRPC desde un explorador,</span><span class="sxs-lookup"><span data-stu-id="6afa9-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="6afa9-148">aunque de manera diferente:</span><span class="sxs-lookup"><span data-stu-id="6afa9-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="6afa9-149">gRPC-Web permite a las aplicaciones de explorador llamar a servicios gRPC desde el explorador con el cliente gRPC-Web y Protobuf.</span><span class="sxs-lookup"><span data-stu-id="6afa9-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="6afa9-150">gRPC-Web requiere que la aplicación de explorador genere un cliente gRPC y tiene la ventaja de que envía mensajes de Protobuf pequeños y rápidos.</span><span class="sxs-lookup"><span data-stu-id="6afa9-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="6afa9-151">La API HTTP de gRPC permite a las aplicaciones de explorador llamar a servicios gRPC como si fueran API de RESTful con JSON.</span><span class="sxs-lookup"><span data-stu-id="6afa9-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="6afa9-152">No es necesario que la aplicación de explorador genere un cliente gRPC o que disponga de información sobre gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="6afa9-153">No se crea ningún cliente generado para la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="6afa9-154">Se puede llamar al servicio `Greeter` anterior mediante las API de JavaScript del explorador:</span><span class="sxs-lookup"><span data-stu-id="6afa9-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="6afa9-155">Estado experimental</span><span class="sxs-lookup"><span data-stu-id="6afa9-155">Experimental status</span></span>

<span data-ttu-id="6afa9-156">La API HTTP de gRPC es un experimento.</span><span class="sxs-lookup"><span data-stu-id="6afa9-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="6afa9-157">Todavía no está completa y no se admite.</span><span class="sxs-lookup"><span data-stu-id="6afa9-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="6afa9-158">Nos interesa esta tecnología y las capacidades que ofrece a los desarrolladores de aplicaciones para crear rápidamente servicios gRPC y JSON al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="6afa9-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="6afa9-159">No hay ningún compromiso para completar la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="6afa9-160">Queremos valorar el interés que tienen los desarrolladores por la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="6afa9-161">Si le atrae la API HTTP de gRPC, [envíenos sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="6afa9-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="6afa9-162">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="6afa9-162">grpc-gateway</span></span>

<span data-ttu-id="6afa9-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) es otra tecnología para crear API JSON de RESTful desde servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="6afa9-164">Usa las mismas anotaciones *.proto* para asignar conceptos HTTP a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="6afa9-165">La principal diferencia entre grpc-gateway y la API HTTP de gRPC es que grpc-gateway usa la generación de código para crear un servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="6afa9-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="6afa9-166">El proxy inverso traslada las llamadas de RESTful a gRPC y, luego, las envía al servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="6afa9-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="6afa9-167">Para obtener información sobre la instalación y el uso de grpc-gateway, consulte el [archivo LÉAME de grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="6afa9-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6afa9-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6afa9-168">Additional resources</span></span>

* [<span data-ttu-id="6afa9-169">Documentación de google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="6afa9-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
