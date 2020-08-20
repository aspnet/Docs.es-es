---
title: Uso de gRPC en aplicaciones de explorador
author: jamesnk
description: Obtenga información sobre cómo configurar servicios gRPC en ASP.NET Core a los que se puede llamar desde aplicaciones del explorador usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 8d1f761731ab3840d009eba1ff5316808bafec40
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634415"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="51f75-103">Uso de gRPC en aplicaciones de explorador</span><span class="sxs-lookup"><span data-stu-id="51f75-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="51f75-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="51f75-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="51f75-105">Obtenga información sobre cómo configurar un servicio gRPC de ASP.NET Core existente al que se puede llamar desde aplicaciones del explorador mediante el protocolo [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md).</span><span class="sxs-lookup"><span data-stu-id="51f75-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="51f75-106">gRPC-Web permite a las aplicaciones de explorador de JavaScript y Blazor llamar a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="51f75-107">No se puede llamar a un servicio HTTP/2 gRPC desde una aplicación basada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="51f75-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="51f75-108">Los servicios gRPC hospedados en ASP.NET Core se pueden configurar para admitir gRPC-Web junto con HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="51f75-109">Para instrucciones sobre cómo agregar un servicio gRPC a una aplicación de ASP.NET Core existente, consulte [Incorporación de servicios gRPC a una aplicación de ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="51f75-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="51f75-110">Para instrucciones sobre cómo crear un proyecto de gRPC, consulte <xref:tutorials/grpc/grpc-start>.</span><span class="sxs-lookup"><span data-stu-id="51f75-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="51f75-111">gRPC-Web en ASP.NET Core frente a Envoy</span><span class="sxs-lookup"><span data-stu-id="51f75-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="51f75-112">Hay dos opciones para agregar gRPC-Web a una aplicación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="51f75-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="51f75-113">Compatibilidad con gRPC-Web junto con HTTP/2 gRPC en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51f75-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="51f75-114">Esta opción usa el middleware que el paquete `Grpc.AspNetCore.Web` proporciona.</span><span class="sxs-lookup"><span data-stu-id="51f75-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="51f75-115">Use la compatibilidad con gRPC-Web del [proxy de Envoy](https://www.envoyproxy.io/) para traducir gRPC-Web a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="51f75-116">A continuación, la llamada traducida se reenvía a la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51f75-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="51f75-117">Cada enfoque tiene ventajas y desventajas.</span><span class="sxs-lookup"><span data-stu-id="51f75-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="51f75-118">Si el entorno de una aplicación ya usa Envoy como proxy, puede que tenga sentido usar Envoy para proporcionar compatibilidad con gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="51f75-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="51f75-119">Para obtener una solución básica para gRPC-Web que solo requiera ASP.NET Core, `Grpc.AspNetCore.Web` es una buena elección.</span><span class="sxs-lookup"><span data-stu-id="51f75-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="51f75-120">Configuración de gRPC-Web en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51f75-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="51f75-121">Los servicios gRPC hospedados en ASP.NET Core se pueden configurar para admitir gRPC-Web junto con HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="51f75-122">gRPC-Web no requiere ningún cambio en los servicios.</span><span class="sxs-lookup"><span data-stu-id="51f75-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="51f75-123">La única modificación es la configuración de inicio.</span><span class="sxs-lookup"><span data-stu-id="51f75-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="51f75-124">Para habilitar gRPC-Web con un servicio gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="51f75-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="51f75-125">Agregue una referencia al paquete [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).</span><span class="sxs-lookup"><span data-stu-id="51f75-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="51f75-126">Configure la aplicación para que use gRPC-Web, agregando para ello `UseGrpcWeb` y `EnableGrpcWeb` a *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="51f75-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="51f75-127">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="51f75-127">The preceding code:</span></span>

* <span data-ttu-id="51f75-128">Agrega el middleware de gRPC-Web, `UseGrpcWeb`, después del enrutamiento y antes de los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="51f75-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="51f75-129">Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite gRPC-Web con `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="51f75-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="51f75-130">Como alternativa, se puede configurar el middleware gRPC-Web de forma que todos los servicios admitan gRPC-Web de forma predeterminada y no se necesite `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="51f75-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="51f75-131">Especifique `new GrpcWebOptions { DefaultEnabled = true }` cuando se agregue el middleware.</span><span class="sxs-lookup"><span data-stu-id="51f75-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="51f75-132">Hay un problema conocido que hace que gRPC-Web genere un error cuando está [hospedado en Http.sys](xref:fundamentals/servers/httpsys) en .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="51f75-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="51f75-133">Una solución alternativa para que gRPC-Web funcione en Http.sys está disponible [aquí](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="51f75-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="51f75-134">gRPC-Web y CORS</span><span class="sxs-lookup"><span data-stu-id="51f75-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="51f75-135">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="51f75-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="51f75-136">Esta restricción se aplica a la hora de realizar llamadas de gRPC-Web con aplicaciones de explorador.</span><span class="sxs-lookup"><span data-stu-id="51f75-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="51f75-137">Por ejemplo, una aplicación de explorador atendida por `https://www.contoso.com` no puede llamar a los servicios gRPC-Web hospedados en `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="51f75-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="51f75-138">Para suavizar esta restricción, podemos recurrir al uso compartido de recursos entre orígenes (CORS).</span><span class="sxs-lookup"><span data-stu-id="51f75-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="51f75-139">Para permitir que una aplicación de explorador haga llamadas de gRPC-Web entre orígenes, configure [CORS en ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="51f75-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="51f75-140">Use la compatibilidad de CORS integrada y exponga los encabezados específicos de gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span><span class="sxs-lookup"><span data-stu-id="51f75-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="51f75-141">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="51f75-141">The preceding code:</span></span>

* <span data-ttu-id="51f75-142">Llama a `AddCors` para agregar los servicios CORS y configura una directiva CORS que expone los encabezados específicos de gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="51f75-143">Llama a `UseCors` para agregar el middleware de CORS después del enrutamiento y antes de los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="51f75-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="51f75-144">Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite CORS con `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="51f75-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="51f75-145">gRPC-Web y streaming</span><span class="sxs-lookup"><span data-stu-id="51f75-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="51f75-146">El gRPC tradicional a través de HTTP/2 admite el streaming en todas las direcciones.</span><span class="sxs-lookup"><span data-stu-id="51f75-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="51f75-147">gRPC-Web ofrece compatibilidad limitada para streaming:</span><span class="sxs-lookup"><span data-stu-id="51f75-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="51f75-148">Los clientes del explorador gRPC-Web no admiten la llamada a métodos de streaming de cliente y streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="51f75-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="51f75-149">Los servicios gRPC de ASP.NET Core hospedados en Azure App Service e IIS no admiten streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="51f75-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="51f75-150">Al usar gRPC-Web, solo se recomienda el uso de métodos unarios y métodos de streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="51f75-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="51f75-151">Llamada a gRPC-Web desde el explorador</span><span class="sxs-lookup"><span data-stu-id="51f75-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="51f75-152">Las aplicaciones de explorador pueden usar gRPC-Web para llamar a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="51f75-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="51f75-153">Existen algunos requisitos y limitaciones a la hora de llamar a servicios gRPC con gRPC-Web desde el explorador:</span><span class="sxs-lookup"><span data-stu-id="51f75-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="51f75-154">El servidor debe estar configurado para admitir gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="51f75-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="51f75-155">No se pueden usar llamadas de streaming de cliente ni de streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="51f75-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="51f75-156">Sí se puede usar el streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="51f75-156">Server streaming is supported.</span></span>
* <span data-ttu-id="51f75-157">Para llamar a servicios gRPC en otro dominio, hay que configurar [CORS](xref:security/cors) en el servidor.</span><span class="sxs-lookup"><span data-stu-id="51f75-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="51f75-158">Cliente gRPC-Web de JavaScript</span><span class="sxs-lookup"><span data-stu-id="51f75-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="51f75-159">Existe un cliente gRPC-Web de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="51f75-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="51f75-160">Para obtener instrucciones sobre cómo usar gRPC-Web en JavaScript, vea [Escribir código de cliente de JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="51f75-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="51f75-161">Configuración de gRPC-Web con el cliente gRPC de .NET</span><span class="sxs-lookup"><span data-stu-id="51f75-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="51f75-162">El cliente gRPC de .NET se puede configurar para realizar llamadas de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="51f75-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="51f75-163">Esto resulta útil en las aplicaciones [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), que se hospedan en el explorador y tienen las mismas limitaciones HTTP de código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="51f75-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="51f75-164">Llamar a gRPC-Web con un cliente .NET es [igual que HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="51f75-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="51f75-165">La única modificación es cómo se crea el canal.</span><span class="sxs-lookup"><span data-stu-id="51f75-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="51f75-166">Para usar gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="51f75-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="51f75-167">Agregue una referencia al paquete [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).</span><span class="sxs-lookup"><span data-stu-id="51f75-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="51f75-168">Asegúrese de que la referencia al paquete [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sea 2.29.0 o superior.</span><span class="sxs-lookup"><span data-stu-id="51f75-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="51f75-169">Configure el canal para que use `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="51f75-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="51f75-170">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="51f75-170">The preceding code:</span></span>

* <span data-ttu-id="51f75-171">Configura un canal para que use gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="51f75-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="51f75-172">Crea un cliente y realiza una llamada usando el canal.</span><span class="sxs-lookup"><span data-stu-id="51f75-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="51f75-173">`GrpcWebHandler` tiene las siguientes opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="51f75-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="51f75-174">**InnerHandler**: elemento <xref:System.Net.Http.HttpMessageHandler> subyacente que realiza la solicitud HTTP de gRPC, por ejemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="51f75-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="51f75-175">**GrpcWebMode**: tipo de enumeración que especifica si el `Content-Type` de la solicitud HTTP gRPC es `application/grpc-web` o `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="51f75-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="51f75-176">`GrpcWebMode.GrpcWeb` configura el contenido que se va a enviar sin codificación.</span><span class="sxs-lookup"><span data-stu-id="51f75-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="51f75-177">Valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="51f75-177">Default value.</span></span>
    * <span data-ttu-id="51f75-178">`GrpcWebMode.GrpcWebText` configura el contenido para que tenga codificación Base64.</span><span class="sxs-lookup"><span data-stu-id="51f75-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="51f75-179">Esto es necesario en las llamadas de streaming de servidor en los exploradores.</span><span class="sxs-lookup"><span data-stu-id="51f75-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="51f75-180">**HttpVersion**: elemento `Version` del protocolo HTTP que se usa para establecer [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) en la solicitud HTTP de gRPC subyacente.</span><span class="sxs-lookup"><span data-stu-id="51f75-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="51f75-181">gRPC-Web no requiere que haya una versión específica y no invalida el valor predeterminado a menos que así se especifique.</span><span class="sxs-lookup"><span data-stu-id="51f75-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="51f75-182">Los clientes de gRPC generados tienen métodos sincrónicos y asincrónicos para llamar a métodos unarios.</span><span class="sxs-lookup"><span data-stu-id="51f75-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="51f75-183">Así, `SayHello` es sincrónico y `SayHelloAsync`, asincrónico.</span><span class="sxs-lookup"><span data-stu-id="51f75-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="51f75-184">La llamada a un método sincrónico en una aplicación Blazor WebAssembly hace que la aplicación deje de responder.</span><span class="sxs-lookup"><span data-stu-id="51f75-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="51f75-185">En Blazor WebAssembly, siempre se deben usar métodos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="51f75-185">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51f75-186">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="51f75-186">Additional resources</span></span>

* [<span data-ttu-id="51f75-187">Proyecto de GitHub de gRPC para clientes web</span><span class="sxs-lookup"><span data-stu-id="51f75-187">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
