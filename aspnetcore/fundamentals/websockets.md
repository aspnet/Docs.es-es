---
title: Compatibilidad con WebSockets en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar WebSockets en ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057783"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="47d06-103">Compatibilidad con WebSockets en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d06-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="47d06-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="47d06-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="47d06-105">En este artículo se ofrece una introducción a WebSockets en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d06-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="47d06-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) es un protocolo que habilita canales de comunicación bidireccional persistentes a través de conexiones TCP.</span><span class="sxs-lookup"><span data-stu-id="47d06-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="47d06-107">Se usa en aplicaciones que sacan partido de comunicaciones rápidas y en tiempo real, como las aplicaciones de chat, panel y juegos.</span><span class="sxs-lookup"><span data-stu-id="47d06-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="47d06-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="47d06-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="47d06-109">[Cómo ejecutar](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="47d06-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="47d06-110">[ASP.NET CoreSignalR](xref:signalr/introduction) es una biblioteca que simplifica la adición de funcionalidad web en tiempo real a las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="47d06-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="47d06-111">Usa WebSockets siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="47d06-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="47d06-112">Para la mayoría de las aplicaciones, se recomienda SignalR en lugar de WebSockets sin procesar.</span><span class="sxs-lookup"><span data-stu-id="47d06-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="47d06-113">SignalR proporciona transporte de reserva para entornos donde WebSockets no está disponible.</span><span class="sxs-lookup"><span data-stu-id="47d06-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="47d06-114">También proporciona un modelo básico de aplicación de llamada a procedimiento remoto.</span><span class="sxs-lookup"><span data-stu-id="47d06-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="47d06-115">Además, en la mayoría de los escenarios, SignalR no tiene ninguna desventaja significativa de rendimiento en comparación con WebSockets sin procesar.</span><span class="sxs-lookup"><span data-stu-id="47d06-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="47d06-116">En algunas aplicaciones, [gRPC en .NET](xref:grpc/index) proporciona una alternativa a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="47d06-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47d06-117">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="47d06-117">Prerequisites</span></span>

* <span data-ttu-id="47d06-118">Cualquier sistema operativo que admita ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="47d06-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="47d06-119">Windows 7/Windows Server 2008 o posterior</span><span class="sxs-lookup"><span data-stu-id="47d06-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="47d06-120">Linux</span><span class="sxs-lookup"><span data-stu-id="47d06-120">Linux</span></span>
  * <span data-ttu-id="47d06-121">macOS</span><span class="sxs-lookup"><span data-stu-id="47d06-121">macOS</span></span>  
* <span data-ttu-id="47d06-122">Si la aplicación se ejecuta en Windows con IIS:</span><span class="sxs-lookup"><span data-stu-id="47d06-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="47d06-123">Windows 8/Windows Server 2012 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="47d06-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="47d06-124">IIS 8/Express IIS 8</span><span class="sxs-lookup"><span data-stu-id="47d06-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="47d06-125">WebSockets debe estar habilitado.</span><span class="sxs-lookup"><span data-stu-id="47d06-125">WebSockets must be enabled.</span></span> <span data-ttu-id="47d06-126">Vea la sección [Compatibilidad con IIS/IIS Express](#iisiis-express-support).</span><span class="sxs-lookup"><span data-stu-id="47d06-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="47d06-127">Si la aplicación se ejecuta en [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="47d06-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="47d06-128">Windows 8/Windows Server 2012 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="47d06-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="47d06-129">Para saber qué exploradores son compatibles, vea https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="47d06-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="47d06-130">Configurar el middleware</span><span class="sxs-lookup"><span data-stu-id="47d06-130">Configure the middleware</span></span>

<span data-ttu-id="47d06-131">Agregue el middleware de WebSockets al método `Configure` de la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="47d06-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="47d06-132">Se pueden configurar estas opciones:</span><span class="sxs-lookup"><span data-stu-id="47d06-132">The following settings can be configured:</span></span>

* <span data-ttu-id="47d06-133">`KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="47d06-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="47d06-134">El valor predeterminado es de dos minutos.</span><span class="sxs-lookup"><span data-stu-id="47d06-134">The default is two minutes.</span></span>
* <span data-ttu-id="47d06-135">`ReceiveBufferSize`: el tamaño del búfer usado para recibir datos.</span><span class="sxs-lookup"><span data-stu-id="47d06-135">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="47d06-136">Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos.</span><span class="sxs-lookup"><span data-stu-id="47d06-136">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="47d06-137">El valor predeterminado es 4 KB.</span><span class="sxs-lookup"><span data-stu-id="47d06-137">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="47d06-138">Se pueden configurar estas opciones:</span><span class="sxs-lookup"><span data-stu-id="47d06-138">The following settings can be configured:</span></span>

* <span data-ttu-id="47d06-139">`KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="47d06-139">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="47d06-140">El valor predeterminado es de dos minutos.</span><span class="sxs-lookup"><span data-stu-id="47d06-140">The default is two minutes.</span></span>
* <span data-ttu-id="47d06-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>: el tamaño del búfer usado para recibir datos.</span><span class="sxs-lookup"><span data-stu-id="47d06-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="47d06-142">Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos.</span><span class="sxs-lookup"><span data-stu-id="47d06-142">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="47d06-143">El valor predeterminado es 4 KB.</span><span class="sxs-lookup"><span data-stu-id="47d06-143">The default is 4 KB.</span></span>
* <span data-ttu-id="47d06-144">`AllowedOrigins` - Una lista de valores de encabezado de origen permitidos para las solicitudes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="47d06-144">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="47d06-145">De forma predeterminada, se permiten todos los orígenes.</span><span class="sxs-lookup"><span data-stu-id="47d06-145">By default, all origins are allowed.</span></span> <span data-ttu-id="47d06-146">Consulte "Restricción de los orígenes de WebSocket" a continuación para obtener información detallada.</span><span class="sxs-lookup"><span data-stu-id="47d06-146">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="47d06-147">Aceptar solicitudes WebSocket</span><span class="sxs-lookup"><span data-stu-id="47d06-147">Accept WebSocket requests</span></span>

<span data-ttu-id="47d06-148">En algún momento posterior del ciclo de solicitudes (más adelante en el método `Configure` o en un método de acción, por ejemplo) debe comprobar si se trata de una solicitud WebSocket y aceptarla.</span><span class="sxs-lookup"><span data-stu-id="47d06-148">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="47d06-149">El siguiente ejemplo se corresponde con un momento más adelante en el método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="47d06-149">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="47d06-150">Una solicitud WebSocket puede proceder de cualquier dirección URL, pero este código de ejemplo solo acepta solicitudes de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="47d06-150">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="47d06-151">Cuando se usa un WebSocket, **debe** mantener la canalización de middleware en ejecución durante la duración de la conexión.</span><span class="sxs-lookup"><span data-stu-id="47d06-151">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="47d06-152">Si intenta enviar o recibir un mensaje de WebSocket después de que finalice la canalización de middleware, es posible que obtenga una excepción como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="47d06-152">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="47d06-153">Si utiliza un servicio en segundo plano para escribir datos en un WebSocket, asegúrese de mantener en ejecución el canal de middleware.</span><span class="sxs-lookup"><span data-stu-id="47d06-153">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="47d06-154">Para ello, utilice un <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="47d06-154">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="47d06-155">Pase `TaskCompletionSource` a su servicio de segundo plano y pídale que llame a <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> cuando termine con WebSocket.</span><span class="sxs-lookup"><span data-stu-id="47d06-155">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="47d06-156">Después, espere (con `await`) la propiedad <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante la solicitud, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="47d06-156">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="47d06-157">La excepción de cierre de WebSocket también puede producirse si la devolución de un método de acción ocurre demasiado pronto.</span><span class="sxs-lookup"><span data-stu-id="47d06-157">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="47d06-158">Al aceptar un socket en un método de acción, espere a que finalice el código que usa el socket antes de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="47d06-158">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="47d06-159">No use nunca `Task.Wait`, `Task.Result` ni llamadas de bloqueo similares para esperar a que se complete el socket, ya que pueden causar graves problemas de subprocesamiento.</span><span class="sxs-lookup"><span data-stu-id="47d06-159">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="47d06-160">Use siempre `await`.</span><span class="sxs-lookup"><span data-stu-id="47d06-160">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="47d06-161">Enviar y recibir mensajes</span><span class="sxs-lookup"><span data-stu-id="47d06-161">Send and receive messages</span></span>

<span data-ttu-id="47d06-162">El método `AcceptWebSocketAsync` actualiza la conexión TCP a una conexión WebSocket y proporciona un objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="47d06-162">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="47d06-163">Use el objeto `WebSocket` para enviar y recibir mensajes.</span><span class="sxs-lookup"><span data-stu-id="47d06-163">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="47d06-164">El código antes mostrado que acepta la solicitud WebSocket pasa el objeto `WebSocket` a un método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="47d06-164">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="47d06-165">El código recibe un mensaje y devuelve inmediatamente el mismo mensaje.</span><span class="sxs-lookup"><span data-stu-id="47d06-165">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="47d06-166">Los mensajes se envían y reciben en un bucle hasta que el cliente cierra la conexión:</span><span class="sxs-lookup"><span data-stu-id="47d06-166">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="47d06-167">Cuando la conexión WebSocket se acepta antes de que el bucle comience, la canalización de middleware finaliza.</span><span class="sxs-lookup"><span data-stu-id="47d06-167">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="47d06-168">Tras cerrar el socket, se desenreda la canalización.</span><span class="sxs-lookup"><span data-stu-id="47d06-168">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="47d06-169">Es decir, la solicitud deja de avanzar en la canalización cuando WebSocket se acepta,</span><span class="sxs-lookup"><span data-stu-id="47d06-169">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="47d06-170">pero cuando el bucle termina y el socket se cierra, la solicitud vuelve a recorrer la canalización.</span><span class="sxs-lookup"><span data-stu-id="47d06-170">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="47d06-171">Control de las desconexiones del cliente</span><span class="sxs-lookup"><span data-stu-id="47d06-171">Handle client disconnects</span></span>

<span data-ttu-id="47d06-172">No se informa automáticamente al servidor cuando el cliente se desconecta debido a la pérdida de conectividad.</span><span class="sxs-lookup"><span data-stu-id="47d06-172">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="47d06-173">El servidor recibe un mensaje de desconexión solo si el cliente lo envía, acción que no se puede realizar si se pierde la conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="47d06-173">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="47d06-174">Si desea realizar alguna acción cuando eso suceda, establezca un tiempo de expiración después de que no se reciba del cliente dentro de un determinado período.</span><span class="sxs-lookup"><span data-stu-id="47d06-174">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="47d06-175">Si el cliente no siempre está enviando mensajes y no quiere que se agote el tiempo de expiración solo porque la conexión está inactiva, haga que el cliente utilice un temporizador para enviar un mensaje de ping cada equis segundos.</span><span class="sxs-lookup"><span data-stu-id="47d06-175">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="47d06-176">En el servidor, si aún no ha llegado un mensaje dentro de 2\*X segundos después del anterior, termine la conexión e informe que ha desconectado el cliente.</span><span class="sxs-lookup"><span data-stu-id="47d06-176">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="47d06-177">Espere el doble del intervalo de tiempo esperado para dejar tiempo extra para los retrasos de la red que podrían retener el mensaje de ping.</span><span class="sxs-lookup"><span data-stu-id="47d06-177">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="47d06-178">Restricción de los orígenes de WebSocket</span><span class="sxs-lookup"><span data-stu-id="47d06-178">WebSocket origin restriction</span></span>

<span data-ttu-id="47d06-179">Las protecciones proporcionadas por CORS no se aplican a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="47d06-179">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="47d06-180">Los exploradores **no** hacen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="47d06-180">Browsers do **not**:</span></span>

* <span data-ttu-id="47d06-181">Efectúan solicitudes preparatorias CORS.</span><span class="sxs-lookup"><span data-stu-id="47d06-181">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="47d06-182">Respetan las restricciones especificadas en los encabezados `Access-Control` al efectuar solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="47d06-182">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="47d06-183">En cambio, sí que envían el encabezado `Origin` al emitir solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="47d06-183">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="47d06-184">Las aplicaciones deben configurarse para validar estos encabezados a fin de garantizar que solo se permitan los WebSockets procedentes de los orígenes esperados.</span><span class="sxs-lookup"><span data-stu-id="47d06-184">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="47d06-185">Si hospeda su servidor en "https://server.com" y su cliente en "https://client.com", agregue "https://client.com" a la lista `AllowedOrigins` de WebSockets para efectuar la comprobación.</span><span class="sxs-lookup"><span data-stu-id="47d06-185">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="47d06-186">El encabezado `Origin` está controlado por el cliente y, al igual que el encabezado `Referer`, se puede falsificar.</span><span class="sxs-lookup"><span data-stu-id="47d06-186">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="47d06-187">**No** use estos encabezados como mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="47d06-187">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="47d06-188">Compatibilidad con IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="47d06-188">IIS/IIS Express support</span></span>

<span data-ttu-id="47d06-189">El protocolo WebSocket se puede usar en Windows Server 2012 o posterior, y en Windows 8 o posterior con IIS o IIS Express 8 o posterior.</span><span class="sxs-lookup"><span data-stu-id="47d06-189">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="47d06-190">WebSocket siempre está habilitado cuando se usa IIS Express.</span><span class="sxs-lookup"><span data-stu-id="47d06-190">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="47d06-191">Habilitación de WebSocket en IIS</span><span class="sxs-lookup"><span data-stu-id="47d06-191">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="47d06-192">Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 2012 o posterior:</span><span class="sxs-lookup"><span data-stu-id="47d06-192">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="47d06-193">Estos pasos no son necesarios cuando se usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="47d06-193">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="47d06-194">Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**.</span><span class="sxs-lookup"><span data-stu-id="47d06-194">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="47d06-195">Seleccione **Instalación basada en características o en roles**.</span><span class="sxs-lookup"><span data-stu-id="47d06-195">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="47d06-196">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="47d06-196">Select **Next**.</span></span>
1. <span data-ttu-id="47d06-197">Seleccione el servidor que corresponda (el servidor local está seleccionado de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="47d06-197">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="47d06-198">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="47d06-198">Select **Next**.</span></span>
1. <span data-ttu-id="47d06-199">Expanda **Servidor web (IIS)** en el árbol **Roles**, expanda **Servidor web** y, por último, expanda **Desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="47d06-199">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="47d06-200">Seleccione **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="47d06-200">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="47d06-201">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="47d06-201">Select **Next**.</span></span>
1. <span data-ttu-id="47d06-202">Si no necesita más características, haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="47d06-202">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="47d06-203">Haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="47d06-203">Select **Install**.</span></span>
1. <span data-ttu-id="47d06-204">Cuando la instalación finalice, haga clic en **Cerrar** para salir del asistente.</span><span class="sxs-lookup"><span data-stu-id="47d06-204">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="47d06-205">Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 8 o posterior:</span><span class="sxs-lookup"><span data-stu-id="47d06-205">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="47d06-206">Estos pasos no son necesarios cuando se usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="47d06-206">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="47d06-207">Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="47d06-207">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="47d06-208">Abra los nodos siguientes: **Internet Information Services** > **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="47d06-208">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="47d06-209">Seleccione la característica **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="47d06-209">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="47d06-210">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="47d06-210">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="47d06-211">Deshabilitar WebSocket al usar socket.io en Node.js</span><span class="sxs-lookup"><span data-stu-id="47d06-211">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="47d06-212">Si usa la compatibilidad de WebSocket en [socket.io](https://socket.io/) en [Node.js](https://nodejs.org/), deshabilite el módulo IIS WebSocket predeterminado, usando para ello el elemento `webSocket` de *web.config* o de *applicationHost.config*. Si este paso no se lleva a cabo, el módulo IIS WebSocket intenta controlar la comunicación de WebSocket en lugar de Node.js y la aplicación.</span><span class="sxs-lookup"><span data-stu-id="47d06-212">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="47d06-213">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="47d06-213">Sample app</span></span>

<span data-ttu-id="47d06-214">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompaña a este artículo es una aplicación de eco.</span><span class="sxs-lookup"><span data-stu-id="47d06-214">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="47d06-215">Tiene una página web que realiza las conexiones WebSocket y el servidor reenvía de vuelta al cliente todos los mensajes que reciba.</span><span class="sxs-lookup"><span data-stu-id="47d06-215">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="47d06-216">Ejecute la aplicación desde un símbolo del sistema (no está configurada para ejecutarse desde Visual Studio con IIS Express) y vaya a http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="47d06-216">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="47d06-217">En la página web se muestra el estado de conexión en la parte superior izquierda:</span><span class="sxs-lookup"><span data-stu-id="47d06-217">The web page shows the connection status in the upper left:</span></span>

![Estado inicial de la página web](websockets/_static/start.png)

<span data-ttu-id="47d06-219">Seleccione **Connect** (Conectar) para enviar una solicitud WebSocket para la URL mostrada.</span><span class="sxs-lookup"><span data-stu-id="47d06-219">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="47d06-220">Escriba un mensaje de prueba y seleccione **Send** (Enviar).</span><span class="sxs-lookup"><span data-stu-id="47d06-220">Enter a test message and select **Send**.</span></span> <span data-ttu-id="47d06-221">Cuando haya terminado, seleccione **Close Socket** (Cerrar socket).</span><span class="sxs-lookup"><span data-stu-id="47d06-221">When done, select **Close Socket**.</span></span> <span data-ttu-id="47d06-222">Los informes de la sección **Communication Log** (Registro de comunicación) informan de cada acción de abrir, enviar y cerrar a medida que se producen.</span><span class="sxs-lookup"><span data-stu-id="47d06-222">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado inicial de la página web](websockets/_static/end.png)

