---
title: Servicios gRPC con ASP.NET Core
author: juntaoluo
description: Conozca los conceptos básicos a la hora de escribir servicios gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530208"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="20812-103">Servicios gRPC con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20812-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="20812-104">En este documento se muestra cómo empezar a usar servicios gRPC con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20812-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="20812-105">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="20812-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20812-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20812-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="20812-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20812-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20812-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="20812-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="20812-109">Introducción al servicio gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20812-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="20812-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="20812-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20812-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20812-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20812-112">Consulte el artículo [Introducción a los servicios gRPC](xref:tutorials/grpc/grpc-start) para obtener instrucciones detalladas sobre cómo crear un proyecto gRPC.</span><span class="sxs-lookup"><span data-stu-id="20812-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20812-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="20812-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="20812-114">Ejecute `dotnet new grpc -o GrpcGreeter` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="20812-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="20812-115">Incorporación de servicios gRPC a una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20812-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="20812-116">gRPC requiere el paquete [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="20812-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="20812-117">Configuración de gRPC</span><span class="sxs-lookup"><span data-stu-id="20812-117">Configure gRPC</span></span>

<span data-ttu-id="20812-118">En *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="20812-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="20812-119">gRPC se habilita con el método `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="20812-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="20812-120">Cada servicio gRPC se agrega a la canalización de enrutamiento a través del método `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="20812-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="20812-121">El middleware y las características de ASP.NET Core comparten la canalización de enrutamiento, por lo que se puede configurar una aplicación para que preste servicio a controladores de solicitudes adicionales.</span><span class="sxs-lookup"><span data-stu-id="20812-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="20812-122">Los controladores de solicitudes adicionales, como los controladores MVC, trabajan en paralelo con los servicios gRPC configurados.</span><span class="sxs-lookup"><span data-stu-id="20812-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="20812-123">Opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="20812-123">Server options</span></span>

<span data-ttu-id="20812-124">Los servicios gRPC se pueden hospedar por todos los servidores de ASP.NET Core integrados.</span><span class="sxs-lookup"><span data-stu-id="20812-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="20812-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="20812-125">Kestrel</span></span>
> * <span data-ttu-id="20812-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="20812-126">TestServer</span></span>
> * <span data-ttu-id="20812-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="20812-127">IIS&dagger;</span></span>
> * <span data-ttu-id="20812-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="20812-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="20812-129">&dagger;IIS requiere .NET 5 y Windows 10, compilación 20241 o posterior.</span><span class="sxs-lookup"><span data-stu-id="20812-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="20812-130">&Dagger;HTTP.sys requiere .NET 5 y Windows 10, compilación 19529 o posterior.</span><span class="sxs-lookup"><span data-stu-id="20812-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="20812-131">Para obtener más información sobre cómo elegir el servidor adecuado para una aplicación ASP.NET Core, vea <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="20812-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="20812-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="20812-132">Kestrel</span></span>

<span data-ttu-id="20812-133">[Kestrel](xref:fundamentals/servers/kestrel) es un servidor web multiplataforma de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20812-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="20812-134">Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys (como el uso compartido de puertos).</span><span class="sxs-lookup"><span data-stu-id="20812-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="20812-135">Los puntos de conexión para gRPC de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="20812-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="20812-136">Requieren HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-136">Require HTTP/2.</span></span>
* <span data-ttu-id="20812-137">Deben protegerse con [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="20812-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="20812-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="20812-138">HTTP/2</span></span>

<span data-ttu-id="20812-139">gRPC requiere HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="20812-140">gRPC para ASP.NET Core valida que [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) sea `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="20812-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="20812-141">Kestrel [admite HTTP/2](xref:fundamentals/servers/kestrel/http2) en la mayoría de los sistemas operativos modernos.</span><span class="sxs-lookup"><span data-stu-id="20812-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="20812-142">Los puntos de conexión de Kestrel se configuran para admitir conexiones HTTP/1.1 y HTTP/2 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="20812-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="20812-143">TLS</span><span class="sxs-lookup"><span data-stu-id="20812-143">TLS</span></span>

<span data-ttu-id="20812-144">Los puntos de conexión de Kestrel usados para gRPC deben protegerse con TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="20812-145">En la fase de desarrollo, se crea automáticamente un punto de conexión protegido con TLS en `https://localhost:5001` cuando el certificado de desarrollo de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="20812-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="20812-146">No se requiere ninguna configuración.</span><span class="sxs-lookup"><span data-stu-id="20812-146">No configuration is required.</span></span> <span data-ttu-id="20812-147">Un prefijo `https` comprueba que el punto de conexión de Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="20812-148">En un entorno de producción, se debe configurar TLS explícitamente.</span><span class="sxs-lookup"><span data-stu-id="20812-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="20812-149">En el siguiente ejemplo de *appsettings.json* , se proporciona un punto de conexión HTTP/2 protegido con TLS:</span><span class="sxs-lookup"><span data-stu-id="20812-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="20812-150">Como alternativa, se pueden configurar puntos de conexión de Kestrel en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="20812-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="20812-151">Negociación del protocolo</span><span class="sxs-lookup"><span data-stu-id="20812-151">Protocol negotiation</span></span>

<span data-ttu-id="20812-152">TLS no solo se usa para proteger la comunicación.</span><span class="sxs-lookup"><span data-stu-id="20812-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="20812-153">El protocolo de enlace [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS se usa para negociar el protocolo de conexión entre el cliente y el servidor cuando un punto de conexión admite varios protocolos.</span><span class="sxs-lookup"><span data-stu-id="20812-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="20812-154">Esta negociación determina si la conexión usa HTTP/1.1 o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="20812-155">Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="20812-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="20812-156">No se puede usar un punto de conexión con varios protocolos (por ejemplo, `HttpProtocols.Http1AndHttp2`) sin TLS, porque no hay ninguna negociación.</span><span class="sxs-lookup"><span data-stu-id="20812-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="20812-157">Se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión no seguro y se produce un error en las llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="20812-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="20812-158">Para obtener más información sobre cómo habilitar HTTP/2 y TLS con Kestrel, consulte la sección sobre la [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="20812-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="20812-159">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="20812-160">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="20812-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="20812-161">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="20812-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="20812-162">IIS</span><span class="sxs-lookup"><span data-stu-id="20812-162">IIS</span></span>

<span data-ttu-id="20812-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) es un servidor web flexible, seguro y administrable para el hospedaje de aplicaciones web, incluido ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20812-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="20812-164">Se requieren .NET 5 y Windows 10, compilación 20241 o posterior, para hospedar servicios de gRPC con IIS.</span><span class="sxs-lookup"><span data-stu-id="20812-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="20812-165">IIS debe estar configurado para usar TLS y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="20812-166">Para obtener más información, vea <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="20812-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="20812-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="20812-167">HTTP.sys</span></span>

<span data-ttu-id="20812-168">[HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor web de ASP.NET Core que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="20812-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="20812-169">Se requieren .NET 5 y Windows 10, compilación 20241 o posterior, para hospedar servicios de gRPC con HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="20812-169">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="20812-170">HTTP.sys debe estar configurado para usar TLS y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="20812-171">Para obtener más información, vea [Compatibilidad con HTTP/2 del servidor web HTTP.sys](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="20812-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="20812-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="20812-172">Kestrel</span></span>

<span data-ttu-id="20812-173">[Kestrel](xref:fundamentals/servers/kestrel) es un servidor web multiplataforma de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20812-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="20812-174">Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys (como el uso compartido de puertos).</span><span class="sxs-lookup"><span data-stu-id="20812-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="20812-175">Los puntos de conexión para gRPC de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="20812-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="20812-176">Requieren HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-176">Require HTTP/2.</span></span>
* <span data-ttu-id="20812-177">Deben protegerse con [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="20812-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="20812-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="20812-178">HTTP/2</span></span>

<span data-ttu-id="20812-179">gRPC requiere HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="20812-180">gRPC para ASP.NET Core valida que [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) sea `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="20812-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="20812-181">Kestrel [admite HTTP/2](xref:fundamentals/servers/kestrel#http2-support) en la mayoría de los sistemas operativos modernos.</span><span class="sxs-lookup"><span data-stu-id="20812-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="20812-182">Los puntos de conexión de Kestrel se configuran para admitir conexiones HTTP/1.1 y HTTP/2 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="20812-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="20812-183">TLS</span><span class="sxs-lookup"><span data-stu-id="20812-183">TLS</span></span>

<span data-ttu-id="20812-184">Los puntos de conexión de Kestrel usados para gRPC deben protegerse con TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="20812-185">En la fase de desarrollo, se crea automáticamente un punto de conexión protegido con TLS en `https://localhost:5001` cuando el certificado de desarrollo de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="20812-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="20812-186">No se requiere ninguna configuración.</span><span class="sxs-lookup"><span data-stu-id="20812-186">No configuration is required.</span></span> <span data-ttu-id="20812-187">Un prefijo `https` comprueba que el punto de conexión de Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="20812-188">En un entorno de producción, se debe configurar TLS explícitamente.</span><span class="sxs-lookup"><span data-stu-id="20812-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="20812-189">En el siguiente ejemplo de *appsettings.json* , se proporciona un punto de conexión HTTP/2 protegido con TLS:</span><span class="sxs-lookup"><span data-stu-id="20812-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="20812-190">Como alternativa, se pueden configurar puntos de conexión de Kestrel en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="20812-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="20812-191">Negociación del protocolo</span><span class="sxs-lookup"><span data-stu-id="20812-191">Protocol negotiation</span></span>

<span data-ttu-id="20812-192">TLS no solo se usa para proteger la comunicación.</span><span class="sxs-lookup"><span data-stu-id="20812-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="20812-193">El protocolo de enlace [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS se usa para negociar el protocolo de conexión entre el cliente y el servidor cuando un punto de conexión admite varios protocolos.</span><span class="sxs-lookup"><span data-stu-id="20812-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="20812-194">Esta negociación determina si la conexión usa HTTP/1.1 o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20812-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="20812-195">Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="20812-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="20812-196">No se puede usar un punto de conexión con varios protocolos (por ejemplo, `HttpProtocols.Http1AndHttp2`) sin TLS, porque no hay ninguna negociación.</span><span class="sxs-lookup"><span data-stu-id="20812-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="20812-197">Se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión no seguro y se produce un error en las llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="20812-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="20812-198">Para obtener más información sobre cómo habilitar HTTP/2 y TLS con Kestrel, consulte la sección sobre la [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="20812-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="20812-199">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="20812-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="20812-200">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="20812-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="20812-201">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="20812-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="20812-202">Integración con las API de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20812-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="20812-203">Los servicios gRPC tienen acceso total a las características de ASP.NET Core, como la [inserción de dependencias](xref:fundamentals/dependency-injection) (ID) y los [registros](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="20812-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="20812-204">Por ejemplo, la implementación de los servicios puede resolver un servicio del registrador desde el contenedor de inserción de dependencias mediante el constructor:</span><span class="sxs-lookup"><span data-stu-id="20812-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="20812-205">De forma predeterminada, la implementación de los servicios gRPC puede resolver otros servicios de inserción de dependencias con cualquier duración (singleton, restringida o transitoria).</span><span class="sxs-lookup"><span data-stu-id="20812-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="20812-206">Resolución de HttpContext en métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="20812-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="20812-207">La API de gRPC proporciona acceso a algunos datos de mensajes HTTP/2, como el método, el host, el encabezado y los finalizadores.</span><span class="sxs-lookup"><span data-stu-id="20812-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="20812-208">El acceso se realiza a través del argumento `ServerCallContext` que se pasa a cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="20812-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="20812-209">`ServerCallContext` no proporciona acceso completo a `HttpContext` en todas las API de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="20812-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="20812-210">Sin embargo, el método de extensión `GetHttpContext` sí proporciona acceso completo al objeto `HttpContext` que representa el mensaje HTTP/2 subyacente en las API de ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="20812-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="20812-211">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="20812-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
