---
title: Activación de middleware basada en Factory en ASP.NET Core
author: rick-anderson
description: Aprenda a usar middleware fuertemente tipado con la implementación de una activación basada en Factory en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: b45633baa804c8210ff00bd1bd8f8877c10581eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634740"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="4b9c2-103">Activación de middleware basada en Factory en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b9c2-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b9c2-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> es un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="4b9c2-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4b9c2-105">Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4b9c2-106">Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4b9c2-107">El middleware se registra como un [servicio con ámbito o transitorio](xref:fundamentals/dependency-injection#service-lifetimes) en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4b9c2-108">Ventajas:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-108">Benefits:</span></span>

* <span data-ttu-id="4b9c2-109">Activación por solicitud de cliente (inyección de servicios con ámbito)</span><span class="sxs-lookup"><span data-stu-id="4b9c2-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4b9c2-110">Tipado fuerte de middleware</span><span class="sxs-lookup"><span data-stu-id="4b9c2-110">Strong typing of middleware</span></span>

<span data-ttu-id="4b9c2-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> se activa por solicitud de cliente (conexión), por lo que los servicios con ámbito se pueden insertar en el constructor del middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4b9c2-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b9c2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4b9c2-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4b9c2-113">IMiddleware</span></span>

<span data-ttu-id="4b9c2-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4b9c2-115">El método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) controla las solicitudes y devuelve una <xref:System.Threading.Tasks.Task> que representa la ejecución del middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4b9c2-116">Middleware activado por convención:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4b9c2-117">Middleware activado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4b9c2-118">Las extensiones se crean para los middlewares:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4b9c2-119">No se pueden pasar objetos al middleware activado por Factory con <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4b9c2-120">El middleware activado por Factory se agrega al contenedor integrado en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4b9c2-121">Ambos middlewares se registran en la canalización de procesamiento de solicitudes en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4b9c2-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4b9c2-122">IMiddlewareFactory</span></span>

<span data-ttu-id="4b9c2-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4b9c2-124">La implementación de Middleware Factory se registra en el contenedor como un servicio con ámbito.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4b9c2-125">La implementación <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> predeterminada, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se encuentra en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="4b9c2-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b9c2-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> es un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="4b9c2-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4b9c2-127">Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4b9c2-128">Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4b9c2-129">El middleware se registra como un [servicio con ámbito o transitorio](xref:fundamentals/dependency-injection#service-lifetimes) en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4b9c2-130">Ventajas:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-130">Benefits:</span></span>

* <span data-ttu-id="4b9c2-131">Activación por solicitud de cliente (inyección de servicios con ámbito)</span><span class="sxs-lookup"><span data-stu-id="4b9c2-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4b9c2-132">Tipado fuerte de middleware</span><span class="sxs-lookup"><span data-stu-id="4b9c2-132">Strong typing of middleware</span></span>

<span data-ttu-id="4b9c2-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> se activa por solicitud de cliente (conexión), por lo que los servicios con ámbito se pueden insertar en el constructor del middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4b9c2-134">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b9c2-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4b9c2-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4b9c2-135">IMiddleware</span></span>

<span data-ttu-id="4b9c2-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4b9c2-137">El método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) controla las solicitudes y devuelve una <xref:System.Threading.Tasks.Task> que representa la ejecución del middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4b9c2-138">Middleware activado por convención:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4b9c2-139">Middleware activado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4b9c2-140">Las extensiones se crean para los middlewares:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4b9c2-141">No se pueden pasar objetos al middleware activado por Factory con <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4b9c2-142">El middleware activado por Factory se agrega al contenedor integrado en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4b9c2-143">Ambos middlewares se registran en la canalización de procesamiento de solicitudes en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4b9c2-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4b9c2-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4b9c2-144">IMiddlewareFactory</span></span>

<span data-ttu-id="4b9c2-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4b9c2-146">La implementación de Middleware Factory se registra en el contenedor como un servicio con ámbito.</span><span class="sxs-lookup"><span data-stu-id="4b9c2-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4b9c2-147">La implementación <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> predeterminada, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se encuentra en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="4b9c2-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4b9c2-148">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b9c2-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
