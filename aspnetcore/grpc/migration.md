---
title: Migración de servicios gRPC de C-core a ASP.NET Core
author: juntaoluo
description: Obtenga información sobre cómo migrar una aplicación gRPC existente basada en C-core para que se ejecute sobre la pila de ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059837"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="152f6-103">Migración de servicios gRPC de C-core a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="152f6-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="152f6-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="152f6-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="152f6-105">Debido a la implementación de la pila subyacente, no todas las características funcionan de la misma manera entre aplicaciones [gRPC basadas en C-core](https://grpc.io/blog/grpc-stacks) y aplicaciones basadas en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="152f6-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="152f6-106">En este documento se destacan las diferencias principales para realizar migraciones entre las dos pilas.</span><span class="sxs-lookup"><span data-stu-id="152f6-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="152f6-107">Duración de la implementación del servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="152f6-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="152f6-108">En la pila de ASP.NET Core, los servicios gRPC se crean de forma predeterminada con una [duración restringida](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="152f6-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="152f6-109">En cambio, gRPC C-core se enlaza de forma predeterminada a un servicio con una [duración singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="152f6-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="152f6-110">Una duración restringida permite que la implementación del servicio resuelva otros servicios con duraciones restringidas.</span><span class="sxs-lookup"><span data-stu-id="152f6-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="152f6-111">Por ejemplo, una duración restringida también puede resolver `DbContext` desde el contenedor de inserción de dependencias a través de la inserción de constructores.</span><span class="sxs-lookup"><span data-stu-id="152f6-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="152f6-112">Si se usa una duración restringida:</span><span class="sxs-lookup"><span data-stu-id="152f6-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="152f6-113">Se crea una instancia de la implementación del servicio para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="152f6-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="152f6-114">No es posible compartir el estado entre solicitudes a través de miembros de instancia en el tipo de implementación.</span><span class="sxs-lookup"><span data-stu-id="152f6-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="152f6-115">Lo normal es almacenar los estados compartidos en un servicio singleton en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="152f6-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="152f6-116">Los estados compartidos almacenados se resuelven en el constructor de la implementación del servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="152f6-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="152f6-117">Para obtener más información sobre la duración de los servicios, consulte <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="152f6-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="152f6-118">Adición de un servicio singleton</span><span class="sxs-lookup"><span data-stu-id="152f6-118">Add a singleton service</span></span>

<span data-ttu-id="152f6-119">Para facilitar la transición de una implementación de gRPC de C-core a ASP.NET Core, se puede cambiar la duración de la implementación del servicio de restringida a singleton.</span><span class="sxs-lookup"><span data-stu-id="152f6-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="152f6-120">Esto implica agregar una instancia de la implementación del servicio al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="152f6-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="152f6-121">Sin embargo, una implementación del servicio con una duración singleton ya no puede resolver los servicios restringidos a través de la inserción de constructores.</span><span class="sxs-lookup"><span data-stu-id="152f6-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="152f6-122">Configuración de las opciones de servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="152f6-122">Configure gRPC services options</span></span>

<span data-ttu-id="152f6-123">En las aplicaciones basadas en C-core, los valores como `grpc.max_receive_message_length` y `grpc.max_send_message_length` se configuran con `ChannelOption` en el momento de [crear la instancia del servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="152f6-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="152f6-124">En ASP.NET Core, gRPC proporciona la configuración a través del tipo `GrpcServiceOptions`.</span><span class="sxs-lookup"><span data-stu-id="152f6-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="152f6-125">Por ejemplo, el tamaño máximo del mensaje entrante del servicio gRPC se puede configurar mediante `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="152f6-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="152f6-126">En el ejemplo siguiente, se cambia el valor predeterminado de `MaxReceiveMessageSize` de 4 MB a 16 MB:</span><span class="sxs-lookup"><span data-stu-id="152f6-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="152f6-127">Para obtener más información sobre la configuración, vea <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="152f6-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="152f6-128">Registro</span><span class="sxs-lookup"><span data-stu-id="152f6-128">Logging</span></span>

<span data-ttu-id="152f6-129">Las aplicaciones basadas en C-core usan `GrpcEnvironment` para [configurar el registrador](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) con fines de depuración.</span><span class="sxs-lookup"><span data-stu-id="152f6-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="152f6-130">La pila de ASP.NET Core proporciona esta funcionalidad a través de la [API de registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="152f6-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="152f6-131">Por ejemplo, se puede agregar un registrador al servicio gRPC mediante la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="152f6-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="152f6-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="152f6-132">HTTPS</span></span>

<span data-ttu-id="152f6-133">Las aplicaciones basadas en C-core configuran HTTPS mediante la [propiedad Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="152f6-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="152f6-134">Se usa un concepto similar para configurar servidores en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="152f6-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="152f6-135">Por ejemplo, Kestrel usa la [configuración de puntos de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) para esta funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="152f6-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="152f6-136">Interceptores de gRPC frente a middleware</span><span class="sxs-lookup"><span data-stu-id="152f6-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="152f6-137">El [middleware](xref:fundamentals/middleware/index) de ASP.NET Core ofrece funcionalidades similares a los interceptores de aplicaciones gRPC basadas en C-core.</span><span class="sxs-lookup"><span data-stu-id="152f6-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="152f6-138">El middleware y los interceptores de ASP.NET Core son conceptualmente similares.</span><span class="sxs-lookup"><span data-stu-id="152f6-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="152f6-139">Ambos:</span><span class="sxs-lookup"><span data-stu-id="152f6-139">Both:</span></span>

* <span data-ttu-id="152f6-140">Se usan para construir una canalización que controla una solicitud gRPC.</span><span class="sxs-lookup"><span data-stu-id="152f6-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="152f6-141">Permiten que se realicen tareas antes o después del siguiente componente de la canalización.</span><span class="sxs-lookup"><span data-stu-id="152f6-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="152f6-142">Proporcionan acceso a `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="152f6-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="152f6-143">En el middleware, `HttpContext` es un parámetro.</span><span class="sxs-lookup"><span data-stu-id="152f6-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="152f6-144">En los interceptores, se puede acceder a `HttpContext` mediante el parámetro `ServerCallContext` con el método de extensión `ServerCallContext.GetHttpContext`.</span><span class="sxs-lookup"><span data-stu-id="152f6-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="152f6-145">Tenga en cuenta que esta característica es específica de los interceptores que se ejecutan en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="152f6-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="152f6-146">Diferencias entre los interceptores de gRPC y el middleware de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="152f6-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="152f6-147">Interceptores:</span><span class="sxs-lookup"><span data-stu-id="152f6-147">Interceptors:</span></span>
  * <span data-ttu-id="152f6-148">Operan en el nivel de abstracción de gRPC mediante la clase [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="152f6-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="152f6-149">Proporcionan acceso a los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="152f6-149">Provide access to:</span></span>
    * <span data-ttu-id="152f6-150">Al mensaje deserializado que se envía a una llamada.</span><span class="sxs-lookup"><span data-stu-id="152f6-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="152f6-151">Al mensaje que devuelve la llamada antes de que se serialice.</span><span class="sxs-lookup"><span data-stu-id="152f6-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="152f6-152">Pueden detectar y controlar las excepciones que se producen en los servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="152f6-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="152f6-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="152f6-153">Middleware:</span></span>
  * <span data-ttu-id="152f6-154">Se ejecuta antes que los interceptores de gRPC.</span><span class="sxs-lookup"><span data-stu-id="152f6-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="152f6-155">Opera en los mensajes HTTP/2 subyacentes.</span><span class="sxs-lookup"><span data-stu-id="152f6-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="152f6-156">Solo puede acceder a los bytes de las secuencias de solicitud y respuesta.</span><span class="sxs-lookup"><span data-stu-id="152f6-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="152f6-157">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="152f6-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
