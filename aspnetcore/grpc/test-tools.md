---
title: Comprobación de servicios gRPC con gRPCurl en ASP.NET Core
author: jamesnk
description: Aprenda a probar servicios con las herramientas de gRPC. gRPCurl una herramienta de línea de comandos para interactuar con los servicios de gRPC. gRPCui es una interfaz de usuario web interactiva.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058797"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="4bf96-105">Comprobación de servicios gRPC con gRPCurl en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bf96-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="4bf96-106">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4bf96-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4bf96-107">Hay herramientas disponibles para gRPC que permiten a los desarrolladores probar servicios sin necesidad de compilar aplicaciones cliente:</span><span class="sxs-lookup"><span data-stu-id="4bf96-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="4bf96-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) es una herramienta de línea de comandos que proporciona interacción con los servicios de gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="4bf96-109">[gRPCui](https://github.com/fullstorydev/grpcui) se basa en gRPCurl, y agrega una interfaz de usuario web interactiva para gRPC similar a herramientas como Postman y la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="4bf96-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="4bf96-110">En este artículo se describe cómo:</span><span class="sxs-lookup"><span data-stu-id="4bf96-110">This article discusses how to:</span></span>

* <span data-ttu-id="4bf96-111">Descargar e instalar gRPCurl y gRPCui.</span><span class="sxs-lookup"><span data-stu-id="4bf96-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="4bf96-112">Configurar la reflexión gRPC con una aplicación gRPC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4bf96-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="4bf96-113">Detectar y probar servicios gRPC con `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="4bf96-114">Interactuar con servicios gRPC a través de un explorador mediante `grpcui`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="4bf96-115">Acerca de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="4bf96-115">About gRPCurl</span></span>

<span data-ttu-id="4bf96-116">gRPCurl es una herramienta de línea de comandos creada por la comunidad de gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="4bf96-117">Sus características incluyen:</span><span class="sxs-lookup"><span data-stu-id="4bf96-117">Its features include:</span></span>

* <span data-ttu-id="4bf96-118">Llamada a servicios gRPC, incluidos los servicios de streaming.</span><span class="sxs-lookup"><span data-stu-id="4bf96-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="4bf96-119">Detección de servicios mediante la [reflexión gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="4bf96-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="4bf96-120">Enumeración y descripción de servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="4bf96-121">Funciona con servidores seguros (TLS) e inseguros (texto sin formato).</span><span class="sxs-lookup"><span data-stu-id="4bf96-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="4bf96-122">Para más información sobre la descarga e instalación de `grpcurl`, consulte la [página principal de GitHub de gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="4bf96-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![Línea de comandos de gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="4bf96-124">Configuración de la reflexión gRPC</span><span class="sxs-lookup"><span data-stu-id="4bf96-124">Set up gRPC reflection</span></span>

<span data-ttu-id="4bf96-125">`grpcurl` debe conocer el contrato de servicios de Protobuf para poder llamarlos.</span><span class="sxs-lookup"><span data-stu-id="4bf96-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="4bf96-126">Existen dos formas de hacerlo:</span><span class="sxs-lookup"><span data-stu-id="4bf96-126">There are two ways to do this:</span></span>

* <span data-ttu-id="4bf96-127">Configurando una [reflexión gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4bf96-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="4bf96-128">gRPCurl detectará automáticamente los contratos de servicio.</span><span class="sxs-lookup"><span data-stu-id="4bf96-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="4bf96-129">Especificando archivos `.proto` en los argumentos de la línea de comandos en gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="4bf96-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="4bf96-130">Es más fácil usar gRPCurl con una reflexión gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="4bf96-131">La reflexión gRPC agrega un nuevo servicio gRPC a la aplicación al que los clientes pueden llamar para detectar servicios.</span><span class="sxs-lookup"><span data-stu-id="4bf96-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="4bf96-132">gRPC de ASP.NET Core tiene compatibilidad integrada con la reflexión gRPC con el paquete [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection).</span><span class="sxs-lookup"><span data-stu-id="4bf96-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="4bf96-133">Para configurar la reflexión en una aplicación:</span><span class="sxs-lookup"><span data-stu-id="4bf96-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="4bf96-134">Agregue una referencia de paquete `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="4bf96-135">Registre la reflexión en `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="4bf96-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="4bf96-136">`AddGrpcReflection` para registrar los servicios que habilitan la reflexión.</span><span class="sxs-lookup"><span data-stu-id="4bf96-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="4bf96-137">`MapGrpcReflectionService` para agregar un punto de conexión de servicio de reflexión.</span><span class="sxs-lookup"><span data-stu-id="4bf96-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="4bf96-138">Cuando la reflexión gRPC está configurada:</span><span class="sxs-lookup"><span data-stu-id="4bf96-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="4bf96-139">Un servicio de reflexión gRPC se agrega a la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="4bf96-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="4bf96-140">Las aplicaciones cliente que admiten la reflexión gRPC pueden llamar al servicio de reflexión para detectar los servicios hospedados por el servidor.</span><span class="sxs-lookup"><span data-stu-id="4bf96-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="4bf96-141">Los servicios gRPC se siguen llamando desde el cliente.</span><span class="sxs-lookup"><span data-stu-id="4bf96-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="4bf96-142">La reflexión solo habilita la detección de servicios, y no omite la seguridad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4bf96-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="4bf96-143">Los puntos de conexión protegidos por la [autenticación y autorización](xref:grpc/authn-and-authz) requieren que el autor de la llamada pase unas credenciales determinadas para poder llamar al punto de conexión correctamente.</span><span class="sxs-lookup"><span data-stu-id="4bf96-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="4bf96-144">Use `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="4bf96-144">Use `grpcurl`</span></span>

<span data-ttu-id="4bf96-145">En el argumento `-help` se explican las opciones de la línea de comandos `grpcurl`:</span><span class="sxs-lookup"><span data-stu-id="4bf96-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="4bf96-146">Detección de servicios</span><span class="sxs-lookup"><span data-stu-id="4bf96-146">Discover services</span></span>

<span data-ttu-id="4bf96-147">Utilice el verbo `describe` para ver los servicios definidos por el servidor:</span><span class="sxs-lookup"><span data-stu-id="4bf96-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="4bf96-148">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4bf96-148">The preceding example:</span></span>

* <span data-ttu-id="4bf96-149">Ejecuta el verbo `describe` en el servidor `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="4bf96-150">Imprime los servicios y métodos devueltos por la reflexión gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="4bf96-151">`Greeter` es un servicio implementado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bf96-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="4bf96-152">`ServerReflection` es el servicio agregado por el paquete `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="4bf96-153">Combine `describe` con un nombre de servicio, método o mensaje para ver sus detalles:</span><span class="sxs-lookup"><span data-stu-id="4bf96-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="4bf96-154">Llamada a servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="4bf96-154">Call gRPC services</span></span>

<span data-ttu-id="4bf96-155">Llame a un servicio gRPC especificando un nombre de método y servicio, junto con un argumento JSON que representa el mensaje de solicitud.</span><span class="sxs-lookup"><span data-stu-id="4bf96-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="4bf96-156">El formato JSON se convierte a Protobuf y se envía al servicio.</span><span class="sxs-lookup"><span data-stu-id="4bf96-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="4bf96-157">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4bf96-157">In the preceding example:</span></span>

* <span data-ttu-id="4bf96-158">El argumento `-d` especifica un mensaje de solicitud con JSON.</span><span class="sxs-lookup"><span data-stu-id="4bf96-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="4bf96-159">Este argumento debe ir delante de la dirección del servidor y el nombre del método.</span><span class="sxs-lookup"><span data-stu-id="4bf96-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="4bf96-160">Llama al método `SayHello` en el servicio `greeter.Greeter`.</span><span class="sxs-lookup"><span data-stu-id="4bf96-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="4bf96-161">Imprime el mensaje de respuesta como JSON.</span><span class="sxs-lookup"><span data-stu-id="4bf96-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="4bf96-162">Acerca de gRPCui</span><span class="sxs-lookup"><span data-stu-id="4bf96-162">About gRPCui</span></span>

<span data-ttu-id="4bf96-163">gRPCui es una interfaz de usuario web interactiva para gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="4bf96-164">Se basa en gRPCurl, y ofrece una interfaz gráfica de usuario para detectar y probar servicios gRPC, de forma similar a herramientas HTTP como Postman o la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="4bf96-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="4bf96-165">Para más información sobre la descarga e instalación de `grpcui`, consulte la [página principal de GitHub de gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="4bf96-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="4bf96-166">Usar `grpcui`</span><span class="sxs-lookup"><span data-stu-id="4bf96-166">Using `grpcui`</span></span>

<span data-ttu-id="4bf96-167">Ejecute `grpcui` con la dirección del servidor con el que se va a interactuar como argumento:</span><span class="sxs-lookup"><span data-stu-id="4bf96-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="4bf96-168">La herramienta inicia una ventana del explorador con la interfaz de usuario web interactiva.</span><span class="sxs-lookup"><span data-stu-id="4bf96-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="4bf96-169">Los servicios gRPC se detectan automáticamente mediante la reflexión de gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bf96-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Interfaz de usuario web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="4bf96-171">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4bf96-171">Additional resources</span></span>

* [<span data-ttu-id="4bf96-172">Página principal de GitHub de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="4bf96-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="4bf96-173">Página principal de GitHub de gRPCui</span><span class="sxs-lookup"><span data-stu-id="4bf96-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
