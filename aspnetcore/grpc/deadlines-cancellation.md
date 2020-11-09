---
title: Servicios gRPC confiables con fechas límite y cancelación
author: jamesnk
description: Aprenda a crear servicios gRPC confiables con fechas límite y cancelación en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059928"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="7e92b-103">Servicios gRPC confiables con fechas límite y cancelación</span><span class="sxs-lookup"><span data-stu-id="7e92b-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="7e92b-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7e92b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7e92b-105">Las fechas límite y la cancelación son características usadas por los clientes de gRPC para anular las llamadas en curso.</span><span class="sxs-lookup"><span data-stu-id="7e92b-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="7e92b-106">En este artículo se explica por qué las fechas límite y la cancelación son importantes y cómo usarlas en las aplicaciones gRPC de .NET.</span><span class="sxs-lookup"><span data-stu-id="7e92b-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="7e92b-107">Fechas límite</span><span class="sxs-lookup"><span data-stu-id="7e92b-107">Deadlines</span></span>

<span data-ttu-id="7e92b-108">Una fecha límite permite a un cliente de gRPC especificar el tiempo que esperará a que se complete una llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="7e92b-109">Cuando se supera una fecha límite, se cancela la llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="7e92b-110">Establecer una fecha límite es importante porque proporciona un límite superior para el tiempo durante el que se puede ejecutar una llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="7e92b-111">Impide que los servicios de comportamiento incorrecto se ejecuten indefinidamente y agoten los recursos del servidor.</span><span class="sxs-lookup"><span data-stu-id="7e92b-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="7e92b-112">Las fechas límite son una herramienta útil para compilar aplicaciones confiables y deben configurarse.</span><span class="sxs-lookup"><span data-stu-id="7e92b-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="7e92b-113">Configuración de fecha límite:</span><span class="sxs-lookup"><span data-stu-id="7e92b-113">Deadline configuration:</span></span>

* <span data-ttu-id="7e92b-114">Una fecha límite se configura mediante `CallOptions.Deadline` cuando se realiza una llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="7e92b-115">No hay ningún valor de fecha límite predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7e92b-115">There is no default deadline value.</span></span> <span data-ttu-id="7e92b-116">Las llamadas a gRPC no tienen límite de tiempo a menos que se especifique una fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="7e92b-117">Una fecha límite es la hora UTC en la que se supera la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="7e92b-118">Por ejemplo, `DateTime.UtcNow.AddSeconds(5)` es una fecha límite de cinco segundos a partir de ahora.</span><span class="sxs-lookup"><span data-stu-id="7e92b-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="7e92b-119">Si se usa una hora anterior o actual, la llamada inmediatamente supera la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="7e92b-120">La fecha límite se envía al servicio con la llamada a gRPC y tanto el cliente como el servicio realizan un seguimiento de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="7e92b-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="7e92b-121">Es posible que una llamada a gRPC se complete en un equipo, pero, en el momento en que se devuelva la respuesta al cliente, se habrá superado la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="7e92b-122">Si se supera una fecha límite, el cliente y el servicio tienen otro comportamiento:</span><span class="sxs-lookup"><span data-stu-id="7e92b-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="7e92b-123">El cliente anula inmediatamente la solicitud HTTP subyacente y produce un error `DeadlineExceeded`.</span><span class="sxs-lookup"><span data-stu-id="7e92b-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="7e92b-124">La aplicación cliente puede optar por detectar el error y mostrar un mensaje de tiempo de espera para el usuario.</span><span class="sxs-lookup"><span data-stu-id="7e92b-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="7e92b-125">En el servidor, se anula la solicitud HTTP de ejecución y se genera [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken).</span><span class="sxs-lookup"><span data-stu-id="7e92b-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="7e92b-126">Aunque la solicitud HTTP se ha anulado, la llamada a gRPC continúa ejecutándose en el servidor hasta que se completa el método.</span><span class="sxs-lookup"><span data-stu-id="7e92b-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="7e92b-127">Es importante que el token de cancelación se pase a métodos asincrónicos para que se cancelen junto con la llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="7e92b-128">Por ejemplo, pasar un token de cancelación a consultas asincrónicas de base de datos y solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e92b-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="7e92b-129">Pasar un token de cancelación permite que la llamada cancelada se complete rápidamente en el servidor y libere recursos para otras llamadas.</span><span class="sxs-lookup"><span data-stu-id="7e92b-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="7e92b-130">Configure `CallOptions.Deadline` para establecer una fecha límite para una llamada a gRPC:</span><span class="sxs-lookup"><span data-stu-id="7e92b-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="7e92b-131">Uso de `ServerCallContext.CancellationToken` en un servicio gRPC:</span><span class="sxs-lookup"><span data-stu-id="7e92b-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="7e92b-132">Propagación de fechas límite</span><span class="sxs-lookup"><span data-stu-id="7e92b-132">Propagating deadlines</span></span>

<span data-ttu-id="7e92b-133">Cuando se realiza una llamada a gRPC desde un servicio gRPC en ejecución, se debe propagar la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="7e92b-134">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7e92b-134">For example:</span></span>

1. <span data-ttu-id="7e92b-135">La aplicación cliente llama a `FrontendService.GetUser` con una fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="7e92b-136">`FrontendService` llama a `UserService.GetUser`.</span><span class="sxs-lookup"><span data-stu-id="7e92b-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="7e92b-137">La fecha límite especificada por el cliente debe especificarse con la nueva llamada a gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e92b-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="7e92b-138">`UserService.GetUser` recibe la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="7e92b-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="7e92b-139">Se agota el tiempo de espera correctamente si se supera la fecha límite de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="7e92b-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="7e92b-140">El contexto de llamada proporciona la fecha límite con `ServerCallContext.Deadline`:</span><span class="sxs-lookup"><span data-stu-id="7e92b-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="7e92b-141">La propagación manual de fechas límite puede resultar complicada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="7e92b-142">La fecha límite debe pasarse a cada llamada y es fácil que se pierda accidentalmente.</span><span class="sxs-lookup"><span data-stu-id="7e92b-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="7e92b-143">Hay disponible una solución automática con la fábrica de cliente de gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e92b-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="7e92b-144">Especifique `EnableCallContextPropagation`:</span><span class="sxs-lookup"><span data-stu-id="7e92b-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="7e92b-145">Propaga automáticamente la fecha límite y el token de cancelación a las llamadas secundarias.</span><span class="sxs-lookup"><span data-stu-id="7e92b-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="7e92b-146">La propagación del contexto de llamada es una excelente manera de garantizar que los escenarios complejos de gRPC anidados siempre propagan la fecha límite y la cancelación.</span><span class="sxs-lookup"><span data-stu-id="7e92b-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="7e92b-147">Para obtener más información, vea <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="7e92b-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="7e92b-148">Cancelación</span><span class="sxs-lookup"><span data-stu-id="7e92b-148">Cancellation</span></span>

<span data-ttu-id="7e92b-149">La cancelación permite a un cliente de gRPC cancelar llamadas de larga duración que ya no son necesarias.</span><span class="sxs-lookup"><span data-stu-id="7e92b-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="7e92b-150">Por ejemplo, una llamada a gRPC que transmite actualizaciones en tiempo real se inicia cuando el usuario visita una página en un sitio web.</span><span class="sxs-lookup"><span data-stu-id="7e92b-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="7e92b-151">La secuencia se debe cancelar cuando el usuario navega fuera de la página.</span><span class="sxs-lookup"><span data-stu-id="7e92b-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="7e92b-152">Una llamada a gRPC se puede cancelar en el cliente pasando un token de cancelación con [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) o llamando a `Dispose` en la llamada.</span><span class="sxs-lookup"><span data-stu-id="7e92b-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="7e92b-153">Los servicios gRPC que se pueden cancelar deben:</span><span class="sxs-lookup"><span data-stu-id="7e92b-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="7e92b-154">Pasar `ServerCallContext.CancellationToken` a métodos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="7e92b-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="7e92b-155">La cancelación de métodos asincrónicos permite que la llamada en el servidor se complete rápidamente.</span><span class="sxs-lookup"><span data-stu-id="7e92b-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="7e92b-156">Propagar el token de cancelación a las llamadas secundarias.</span><span class="sxs-lookup"><span data-stu-id="7e92b-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="7e92b-157">La propagación del token de cancelación garantiza que las llamadas secundarias se cancelan con su elemento primario.</span><span class="sxs-lookup"><span data-stu-id="7e92b-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="7e92b-158">La [fábrica de cliente de gRPC](xref:grpc/clientfactory) con `EnableCallContextPropagation()` propaga automáticamente el token de cancelación.</span><span class="sxs-lookup"><span data-stu-id="7e92b-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e92b-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7e92b-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
