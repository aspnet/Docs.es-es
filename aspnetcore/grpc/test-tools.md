---
title: Prueba de los servicios con las herramientas gRPC
author: jamesnk
description: Aprenda a probar servicios con las herramientas de gRPC. gRPCurl una herramienta de línea de comandos para interactuar con los servicios de gRPC. gRPCui es una interfaz de usuario web interactiva.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594390"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="9b54d-105">Prueba de los servicios con las herramientas gRPC</span><span class="sxs-lookup"><span data-stu-id="9b54d-105">Test services with gRPC tools</span></span>

<span data-ttu-id="9b54d-106">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9b54d-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9b54d-107">Hay herramientas disponibles para gRPC que permiten a los desarrolladores probar servicios sin necesidad de compilar aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="9b54d-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="9b54d-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) es una herramienta de línea de comandos que proporciona interacción con los servicios de gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="9b54d-109">[gRPCui](https://github.com/fullstorydev/grpcui) agrega una interfaz de usuario web interactiva para gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="9b54d-110">En este artículo se describe cómo:</span><span class="sxs-lookup"><span data-stu-id="9b54d-110">This article discusses how to:</span></span>

* <span data-ttu-id="9b54d-111">Descargar e instalar gRPCurl y gRPCui.</span><span class="sxs-lookup"><span data-stu-id="9b54d-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="9b54d-112">Configurar la reflexión gRPC con una aplicación gRPC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b54d-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="9b54d-113">Detectar y probar servicios gRPC con `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="9b54d-114">Interactuar con servicios gRPC a través de un explorador mediante `grpcui`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="9b54d-115">Acerca de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="9b54d-115">About gRPCurl</span></span>

<span data-ttu-id="9b54d-116">gRPCurl es una herramienta de línea de comandos creada por la comunidad de gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="9b54d-117">Sus características incluyen:</span><span class="sxs-lookup"><span data-stu-id="9b54d-117">Its features include:</span></span>

* <span data-ttu-id="9b54d-118">Llamada a servicios gRPC, incluidos los servicios de streaming.</span><span class="sxs-lookup"><span data-stu-id="9b54d-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="9b54d-119">Detección de servicios mediante la [reflexión gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="9b54d-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="9b54d-120">Enumeración y descripción de servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="9b54d-121">Funciona con servidores seguros (TLS) e inseguros (texto sin formato).</span><span class="sxs-lookup"><span data-stu-id="9b54d-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="9b54d-122">Para más información sobre la descarga e instalación de `grpcurl`, consulte la [página principal de GitHub de gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="9b54d-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="9b54d-123">Configuración de la reflexión gRPC</span><span class="sxs-lookup"><span data-stu-id="9b54d-123">Setup gRPC reflection</span></span>

<span data-ttu-id="9b54d-124">`grpcurl` debe conocer el contrato de servicios de Protobuf para poder llamarlos.</span><span class="sxs-lookup"><span data-stu-id="9b54d-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="9b54d-125">Existen dos formas de hacerlo:</span><span class="sxs-lookup"><span data-stu-id="9b54d-125">There are two ways to do this:</span></span>

* <span data-ttu-id="9b54d-126">Usar la reflexión gRPC para detectar contratos de servicio.</span><span class="sxs-lookup"><span data-stu-id="9b54d-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="9b54d-127">Especifique archivos *.proto* en argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="9b54d-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="9b54d-128">Es más fácil usar gRPCurl con la reflexión y la detección de servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="9b54d-129">gRPC de ASP.NET Core tiene compatibilidad integrada con la reflexión gRPC con el paquete [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection).</span><span class="sxs-lookup"><span data-stu-id="9b54d-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="9b54d-130">Para configurar la reflexión en una aplicación:</span><span class="sxs-lookup"><span data-stu-id="9b54d-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="9b54d-131">Agregue la referencia de paquete `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="9b54d-132">Registre la reflexión en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b54d-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="9b54d-133">`AddGrpcReflection` para registrar los servicios que habilitan la reflexión.</span><span class="sxs-lookup"><span data-stu-id="9b54d-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="9b54d-134">`MapGrpcReflectionService` para agregar un punto de conexión de servicio de reflexión.</span><span class="sxs-lookup"><span data-stu-id="9b54d-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="9b54d-135">Use `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="9b54d-135">Use `grpcurl`</span></span>

<span data-ttu-id="9b54d-136">En el argumento `-help` se explican las opciones de la línea de comandos `grpcurl`:</span><span class="sxs-lookup"><span data-stu-id="9b54d-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="9b54d-137">Detección de servicios</span><span class="sxs-lookup"><span data-stu-id="9b54d-137">Discover services</span></span>

<span data-ttu-id="9b54d-138">Utilice el verbo `describe` para ver los servicios definidos por el servidor:</span><span class="sxs-lookup"><span data-stu-id="9b54d-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="9b54d-139">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9b54d-139">The preceding example:</span></span>

* <span data-ttu-id="9b54d-140">Ejecuta el verbo `describe` en el servidor `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="9b54d-141">Imprime los servicios y métodos devueltos por la reflexión gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="9b54d-142">`Greeter` es un servicio implementado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b54d-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="9b54d-143">`ServerReflection` es el servicio agregado por el paquete `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="9b54d-144">Combine `describe` con un nombre de servicio, método o mensaje para ver sus detalles:</span><span class="sxs-lookup"><span data-stu-id="9b54d-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="9b54d-145">Llamada a servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="9b54d-145">Call gRPC services</span></span>

<span data-ttu-id="9b54d-146">Llame a un servicio gRPC especificando un nombre de método y servicio, junto con un argumento JSON que representa el mensaje de solicitud.</span><span class="sxs-lookup"><span data-stu-id="9b54d-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="9b54d-147">El formato JSON se convierte a Protobuf y se envía al servicio.</span><span class="sxs-lookup"><span data-stu-id="9b54d-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="9b54d-148">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9b54d-148">The preceding example:</span></span>

* <span data-ttu-id="9b54d-149">El argumento `-d` especifica un mensaje de solicitud con JSON.</span><span class="sxs-lookup"><span data-stu-id="9b54d-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="9b54d-150">Este argumento debe ir delante de la dirección del servidor y el nombre del método.</span><span class="sxs-lookup"><span data-stu-id="9b54d-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="9b54d-151">Llama al método `SayHello` en el servicio `greeter.Greeter`.</span><span class="sxs-lookup"><span data-stu-id="9b54d-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="9b54d-152">Imprime el mensaje de respuesta como JSON.</span><span class="sxs-lookup"><span data-stu-id="9b54d-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="9b54d-153">Acerca de gRPCui</span><span class="sxs-lookup"><span data-stu-id="9b54d-153">About gRPCui</span></span>

<span data-ttu-id="9b54d-154">gRPCui es una interfaz de usuario web interactiva para gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="9b54d-155">Se basa en gRPCurl y ofrece una interfaz gráfica de usuario para detectar y probar servicios gRPC, de forma similar a herramientas HTTP como Postman.</span><span class="sxs-lookup"><span data-stu-id="9b54d-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="9b54d-156">Para más información sobre la descarga e instalación de `grpcui`, consulte la [página principal de GitHub de gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="9b54d-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="9b54d-157">Usar `grpcui`</span><span class="sxs-lookup"><span data-stu-id="9b54d-157">Using `grpcui`</span></span>

<span data-ttu-id="9b54d-158">Ejecute `grpcui` con la dirección del servidor con el que se va a interactuar como argumento.</span><span class="sxs-lookup"><span data-stu-id="9b54d-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="9b54d-159">La herramienta iniciará una ventana del explorador con la interfaz de usuario web interactiva.</span><span class="sxs-lookup"><span data-stu-id="9b54d-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="9b54d-160">Los servicios gRPC se detectan automáticamente mediante la reflexión de gRPC.</span><span class="sxs-lookup"><span data-stu-id="9b54d-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Interfaz de usuario web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="9b54d-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b54d-162">Additional resources</span></span>

* [<span data-ttu-id="9b54d-163">Página principal de GitHub de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="9b54d-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="9b54d-164">Página principal de GitHub de gRPCui</span><span class="sxs-lookup"><span data-stu-id="9b54d-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="9b54d-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="9b54d-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
