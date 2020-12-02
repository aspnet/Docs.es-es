---
title: API JSON básicas con Route-to-code en ASP.net Core
author: jamesnk
description: Obtenga información sobre cómo usar Route-to-code los métodos de extensión JSON y para crear API Web JSON ligeras.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513101"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="d8b94-103">API JSON básicas con Route-to-code en ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d8b94-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="d8b94-104">Por [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d8b94-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="d8b94-105">ASP.NET Core admite varias maneras de crear API Web JSON:</span><span class="sxs-lookup"><span data-stu-id="d8b94-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="d8b94-106">[ASP.net Core API Web](xref:web-api/index) proporciona un marco de trabajo completo para crear API.</span><span class="sxs-lookup"><span data-stu-id="d8b94-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="d8b94-107">Un servicio se crea heredando de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="d8b94-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="d8b94-108">Algunas de las características proporcionadas por el marco incluyen el enlace de modelos, la validación, la negociación de contenido, el formato de entrada y salida, y OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d8b94-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="d8b94-109">Route-to-code es una alternativa que no es de marco a ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="d8b94-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="d8b94-110">Route-to-code conecta el [enrutamiento de ASP.net Core](xref:fundamentals/routing) directamente al código.</span><span class="sxs-lookup"><span data-stu-id="d8b94-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="d8b94-111">El código Lee de la solicitud y escribe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d8b94-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="d8b94-112">Route-to-code no tiene las características avanzadas de la API Web, pero tampoco hay ninguna configuración necesaria para usarla.</span><span class="sxs-lookup"><span data-stu-id="d8b94-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="d8b94-113">Route-to-code es un buen enfoque a la hora de compilar API Web JSON pequeñas y básicas.</span><span class="sxs-lookup"><span data-stu-id="d8b94-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="d8b94-114">Creación de API Web JSON</span><span class="sxs-lookup"><span data-stu-id="d8b94-114">Create JSON web APIs</span></span>

<span data-ttu-id="d8b94-115">ASP.NET Core proporciona métodos auxiliares que facilitan la creación de API Web JSON:</span><span class="sxs-lookup"><span data-stu-id="d8b94-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="d8b94-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> comprueba el `Content-Type` encabezado para un tipo de contenido JSON.</span><span class="sxs-lookup"><span data-stu-id="d8b94-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="d8b94-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> Lee JSON de la solicitud y lo deserializa en el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="d8b94-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="d8b94-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> escribe el valor especificado como JSON en el cuerpo de la respuesta y establece el tipo de contenido de respuesta en `application/json` .</span><span class="sxs-lookup"><span data-stu-id="d8b94-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="d8b94-119">Las API de JSON basadas en rutas ligeras se especifican en *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="d8b94-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="d8b94-120">La ruta y la lógica de la API se configuran en <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> como parte de la canalización de solicitudes de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8b94-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="d8b94-121">Escribir respuesta JSON</span><span class="sxs-lookup"><span data-stu-id="d8b94-121">Write JSON response</span></span>

<span data-ttu-id="d8b94-122">Considere el siguiente código que configura una API JSON para una aplicación:</span><span class="sxs-lookup"><span data-stu-id="d8b94-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="d8b94-123">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d8b94-123">The preceding code:</span></span>

* <span data-ttu-id="d8b94-124">Agrega un punto de conexión de API HTTP GET con `/hello/{name:alpha}` como plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="d8b94-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="d8b94-125">Cuando se encuentra una coincidencia con la ruta, la API lee el `name` valor de ruta de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d8b94-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="d8b94-126">Escribe un tipo anónimo como respuesta JSON con `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="d8b94-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="d8b94-127">Leer solicitud JSON</span><span class="sxs-lookup"><span data-stu-id="d8b94-127">Read JSON request</span></span>

<span data-ttu-id="d8b94-128">`HasJsonContentType` y `ReadFromJsonAsync` se pueden usar para deserializar una respuesta JSON en una API JSON basada en rutas:</span><span class="sxs-lookup"><span data-stu-id="d8b94-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="d8b94-129">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d8b94-129">The preceding code:</span></span>

* <span data-ttu-id="d8b94-130">Agrega un punto de conexión de API HTTP POST con `/weather` como plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="d8b94-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="d8b94-131">Cuando se encuentra una coincidencia con la ruta, `HasJsonContentType` valida el tipo de contenido de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d8b94-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="d8b94-132">Un tipo de contenido no JSON devuelve un código de estado 415.</span><span class="sxs-lookup"><span data-stu-id="d8b94-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="d8b94-133">Si el tipo de contenido es JSON, se deserializa el contenido de la solicitud `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="d8b94-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="d8b94-134">Configurar la serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="d8b94-134">Configure JSON serialization</span></span>

<span data-ttu-id="d8b94-135">Hay dos maneras de personalizar la serialización de JSON:</span><span class="sxs-lookup"><span data-stu-id="d8b94-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="d8b94-136">Las opciones de serialización predeterminadas se pueden configurar con `JsonOptions` en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d8b94-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d8b94-137">`WriteAsJsonAsync` y `ReadFromJsonAsync` tienen sobrecargas que aceptan un `JsonSerializerOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="d8b94-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="d8b94-138">Este `JsonSerializerOptions` objeto invalida las opciones predeterminadas.</span><span class="sxs-lookup"><span data-stu-id="d8b94-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="d8b94-139">Autenticación y autorización</span><span class="sxs-lookup"><span data-stu-id="d8b94-139">Authentication and authorization</span></span>

<span data-ttu-id="d8b94-140">Route-to-code admite la autenticación y autorización.</span><span class="sxs-lookup"><span data-stu-id="d8b94-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="d8b94-141">Los atributos, como `[Authorize]` y `[AllowAnonymous]` , no se pueden colocar en puntos de conexión que se asignan a un delegado de solicitud.</span><span class="sxs-lookup"><span data-stu-id="d8b94-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="d8b94-142">En su lugar, los metadatos de autorización se agregan mediante los `RequireAuthorization` `AllowAnonymous` métodos de extensión y.</span><span class="sxs-lookup"><span data-stu-id="d8b94-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="d8b94-143">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="d8b94-143">Dependency injection</span></span>

<span data-ttu-id="d8b94-144">La [inserción de dependencias (di)](xref:fundamentals/dependency-injection) mediante un constructor no es posible con Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="d8b94-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="d8b94-145">Web API crea un controlador para usted con los servicios insertados en el constructor.</span><span class="sxs-lookup"><span data-stu-id="d8b94-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="d8b94-146">Un tipo no se crea cuando se ejecuta un punto de conexión, por lo que los servicios se deben resolver manualmente.</span><span class="sxs-lookup"><span data-stu-id="d8b94-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="d8b94-147">Las API basadas en rutas pueden usar <xref:System.IServiceProvider> para resolver servicios:</span><span class="sxs-lookup"><span data-stu-id="d8b94-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="d8b94-148">Los servicios de duración transitoria y de ámbito, como `DbContext` , se deben resolver desde [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dentro del delegado de solicitud de un punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="d8b94-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="d8b94-149">Los servicios de duración de singleton, como `ILogger` , se pueden resolver desde [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="d8b94-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="d8b94-150">Los servicios se pueden resolver fuera de los delegados de solicitud y compartirse entre los extremos.</span><span class="sxs-lookup"><span data-stu-id="d8b94-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="d8b94-151">Las API que utilizan DI extensamente deberían considerar el uso de un tipo de aplicación ASP.NET Core que admita DI.</span><span class="sxs-lookup"><span data-stu-id="d8b94-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="d8b94-152">Por ejemplo, ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="d8b94-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="d8b94-153">La inserción de servicios mediante el constructor de un controlador es más fácil que resolver los servicios de forma manual.</span><span class="sxs-lookup"><span data-stu-id="d8b94-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="d8b94-154">Estructura del proyecto de API</span><span class="sxs-lookup"><span data-stu-id="d8b94-154">API project structure</span></span>

<span data-ttu-id="d8b94-155">No es necesario que las API basadas en enrutamiento se encuentren en *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="d8b94-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="d8b94-156">Las API se pueden colocar en otros archivos y asignar al inicio con `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="d8b94-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="d8b94-157">Este enfoque reduce el tamaño del archivo de configuración de inicio.</span><span class="sxs-lookup"><span data-stu-id="d8b94-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="d8b94-158">Considere la siguiente `UserApi` clase estática que define un `Map` método.</span><span class="sxs-lookup"><span data-stu-id="d8b94-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="d8b94-159">El método asigna las API basadas en rutas.</span><span class="sxs-lookup"><span data-stu-id="d8b94-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="d8b94-160">En el `Startup.Configure` método, `Map` se llama al método y a los métodos estáticos de la clase en `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="d8b94-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="d8b94-161">Características destacadas que faltan en comparación con Web API</span><span class="sxs-lookup"><span data-stu-id="d8b94-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="d8b94-162">Route-to-code está diseñado para las API básicas de JSON.</span><span class="sxs-lookup"><span data-stu-id="d8b94-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="d8b94-163">No es compatible con muchas de las características avanzadas que proporciona ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="d8b94-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="d8b94-164">Entre las características no proporcionadas por se Route-to-code incluyen:</span><span class="sxs-lookup"><span data-stu-id="d8b94-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="d8b94-165">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="d8b94-165">Model binding</span></span>
* <span data-ttu-id="d8b94-166">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="d8b94-166">Model validation</span></span>
* <span data-ttu-id="d8b94-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="d8b94-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="d8b94-168">Negociación de contenido</span><span class="sxs-lookup"><span data-stu-id="d8b94-168">Content negotiation</span></span>
* <span data-ttu-id="d8b94-169">Inserción de dependencias de constructor</span><span class="sxs-lookup"><span data-stu-id="d8b94-169">Constructor dependency injection</span></span>
* <span data-ttu-id="d8b94-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span><span class="sxs-lookup"><span data-stu-id="d8b94-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span></span>

<span data-ttu-id="d8b94-171">Considere la posibilidad de usar [ASP.net Core API Web](xref:web-api/index) para crear una API si requiere algunas de las características de la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="d8b94-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8b94-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d8b94-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
