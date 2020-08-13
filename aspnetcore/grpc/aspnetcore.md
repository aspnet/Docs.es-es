---
title: Servicios gRPC con ASP.NET Core
author: juntaoluo
description: Conozca los conceptos básicos a la hora de escribir servicios gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
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
uid: grpc/aspnetcore
ms.openlocfilehash: bd97ece1d42b1b90d0b8740e33924d80b91bf49a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016445"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="1d2ec-103">Servicios gRPC con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d2ec-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="1d2ec-104">En este documento se muestra cómo empezar a usar servicios gRPC con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="1d2ec-105">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1d2ec-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2ec-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2ec-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2ec-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2ec-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2ec-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1d2ec-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="1d2ec-109">Introducción al servicio gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d2ec-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="1d2ec-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2ec-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2ec-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1d2ec-112">Consulte el artículo [Introducción a los servicios gRPC](xref:tutorials/grpc/grpc-start) para obtener instrucciones detalladas sobre cómo crear un proyecto gRPC.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1d2ec-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1d2ec-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1d2ec-114">Ejecute `dotnet new grpc -o GrpcGreeter` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="1d2ec-115">Incorporación de servicios gRPC a una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d2ec-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="1d2ec-116">gRPC requiere el paquete [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="1d2ec-117">Configuración de gRPC</span><span class="sxs-lookup"><span data-stu-id="1d2ec-117">Configure gRPC</span></span>

<span data-ttu-id="1d2ec-118">En *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="1d2ec-119">gRPC se habilita con el método `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="1d2ec-120">Cada servicio gRPC se agrega a la canalización de enrutamiento a través del método `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1d2ec-121">Los middlewares y las características de ASP.NET Core comparten la canalización de enrutamiento, por lo que se puede configurar una aplicación para que preste servicio a controladores de solicitudes adicionales.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="1d2ec-122">Los controladores de solicitudes adicionales, como los controladores MVC, trabajan en paralelo con los servicios gRPC configurados.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="1d2ec-123">Configuración de Kestrel</span><span class="sxs-lookup"><span data-stu-id="1d2ec-123">Configure Kestrel</span></span>

<span data-ttu-id="1d2ec-124">Los puntos de conexión para gRPC de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="1d2ec-125">Requieren HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-125">Require HTTP/2.</span></span>
* <span data-ttu-id="1d2ec-126">Deben protegerse con [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="1d2ec-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1d2ec-127">HTTP/2</span></span>

<span data-ttu-id="1d2ec-128">gRPC requiere HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="1d2ec-129">gRPC para ASP.NET Core valida que [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sea `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="1d2ec-130">Kestrel [admite HTTP/2](xref:fundamentals/servers/kestrel#http2-support) en la mayoría de los sistemas operativos modernos.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="1d2ec-131">Los puntos de conexión de Kestrel se configuran para admitir conexiones HTTP/1.1 y HTTP/2 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="1d2ec-132">TLS</span><span class="sxs-lookup"><span data-stu-id="1d2ec-132">TLS</span></span>

<span data-ttu-id="1d2ec-133">Los puntos de conexión de Kestrel usados para gRPC deben protegerse con TLS.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="1d2ec-134">En la fase de desarrollo, se crea automáticamente un punto de conexión protegido con TLS en `https://localhost:5001` cuando el certificado de desarrollo de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="1d2ec-135">No se requiere ninguna configuración.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-135">No configuration is required.</span></span> <span data-ttu-id="1d2ec-136">Un prefijo `https` comprueba que el punto de conexión de Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="1d2ec-137">En un entorno de producción, se debe configurar TLS explícitamente.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="1d2ec-138">En el siguiente ejemplo de *appsettings.json*, se proporciona un punto de conexión HTTP/2 protegido con TLS:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="1d2ec-139">Como alternativa, se pueden configurar puntos de conexión de Kestrel en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="1d2ec-140">Negociación del protocolo</span><span class="sxs-lookup"><span data-stu-id="1d2ec-140">Protocol negotiation</span></span>

<span data-ttu-id="1d2ec-141">TLS no solo se usa para proteger la comunicación.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="1d2ec-142">El protocolo de enlace [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS se usa para negociar el protocolo de conexión entre el cliente y el servidor cuando un punto de conexión admite varios protocolos.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="1d2ec-143">Esta negociación determina si la conexión usa HTTP/1.1 o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="1d2ec-144">Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="1d2ec-145">No se puede usar un punto de conexión con varios protocolos (por ejemplo, `HttpProtocols.Http1AndHttp2`) sin TLS, porque no hay ninguna negociación.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="1d2ec-146">Se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión no seguro y se produce un error en las llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="1d2ec-147">Para obtener más información sobre cómo habilitar HTTP/2 y TLS con Kestrel, consulte la sección sobre la [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="1d2ec-148">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="1d2ec-149">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="1d2ec-150">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="1d2ec-151">Integración con las API de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d2ec-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="1d2ec-152">Los servicios gRPC tienen acceso total a las características de ASP.NET Core, como la [inserción de dependencias](xref:fundamentals/dependency-injection) (ID) y los [registros](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1d2ec-153">Por ejemplo, la implementación de los servicios puede resolver un servicio del registrador desde el contenedor de inserción de dependencias mediante el constructor:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="1d2ec-154">De forma predeterminada, la implementación de los servicios gRPC puede resolver otros servicios de inserción de dependencias con cualquier duración (singleton, restringida o transitoria).</span><span class="sxs-lookup"><span data-stu-id="1d2ec-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="1d2ec-155">Resolución de HttpContext en métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="1d2ec-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="1d2ec-156">La API de gRPC proporciona acceso a algunos datos de mensajes HTTP/2, como el método, el host, el encabezado y los finalizadores.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="1d2ec-157">El acceso se realiza a través del argumento `ServerCallContext` que se pasa a cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="1d2ec-158">`ServerCallContext` no proporciona acceso completo a `HttpContext` en todas las API de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1d2ec-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="1d2ec-159">Sin embargo, el método de extensión `GetHttpContext` sí proporciona acceso completo al objeto `HttpContext` que representa el mensaje HTTP/2 subyacente en las API de ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="1d2ec-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="1d2ec-160">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1d2ec-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
