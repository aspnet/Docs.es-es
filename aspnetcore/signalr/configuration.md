---
title: Configuración de SignalR en ASP.NET Core
author: bradygaster
description: Aprenda a configurar ASP.NET Core SignalR aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972072"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="78148-103">Configuración de SignalR en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78148-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="78148-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="78148-105">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="78148-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="78148-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="78148-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="78148-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="78148-108">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="78148-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="78148-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="78148-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="78148-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="78148-111">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="78148-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="78148-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78148-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="78148-113">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="78148-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="78148-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="78148-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="78148-116">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="78148-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="78148-117">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="78148-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="78148-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-118">MessagePack serialization options</span></span>

<span data-ttu-id="78148-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="78148-120">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="78148-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="78148-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="78148-122">Configure server options</span></span>

<span data-ttu-id="78148-123">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="78148-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="78148-124">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-124">Option</span></span> | <span data-ttu-id="78148-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-125">Default Value</span></span> | <span data-ttu-id="78148-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="78148-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-127">30 seconds</span></span> | <span data-ttu-id="78148-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="78148-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="78148-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="78148-130">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="78148-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-131">15 seconds</span></span> | <span data-ttu-id="78148-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="78148-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="78148-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-134">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-135">15 seconds</span></span> | <span data-ttu-id="78148-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="78148-137">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="78148-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="78148-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="78148-139">All installed protocols</span></span> | <span data-ttu-id="78148-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="78148-140">Protocols supported by this hub.</span></span> <span data-ttu-id="78148-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="78148-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="78148-142">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="78148-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="78148-143">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="78148-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="78148-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="78148-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="78148-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-146">32 KB</span></span> | <span data-ttu-id="78148-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="78148-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="78148-148">1</span><span class="sxs-lookup"><span data-stu-id="78148-148">1</span></span> | <span data-ttu-id="78148-149">Número máximo de métodos de concentrador a los que cada cliente puede llamar en paralelo antes de poner en cola.</span><span class="sxs-lookup"><span data-stu-id="78148-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="78148-150">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="78148-151">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="78148-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="78148-152">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="78148-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="78148-153">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="78148-154">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="78148-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="78148-155">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="78148-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="78148-156">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-156">Option</span></span> | <span data-ttu-id="78148-157">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-157">Default Value</span></span> | <span data-ttu-id="78148-158">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="78148-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-159">32 KB</span></span> | <span data-ttu-id="78148-160">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="78148-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="78148-161">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="78148-162">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="78148-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="78148-163">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="78148-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="78148-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-164">32 KB</span></span> | <span data-ttu-id="78148-165">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="78148-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="78148-166">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="78148-167">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="78148-167">All Transports are enabled.</span></span> | <span data-ttu-id="78148-168">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="78148-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="78148-169">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-169">See below.</span></span> | <span data-ttu-id="78148-170">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="78148-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="78148-171">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-171">See below.</span></span> | <span data-ttu-id="78148-172">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="78148-173">0</span><span class="sxs-lookup"><span data-stu-id="78148-173">0</span></span> | <span data-ttu-id="78148-174">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="78148-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="78148-175">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="78148-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="78148-176">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="78148-177">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-177">Option</span></span> | <span data-ttu-id="78148-178">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-178">Default Value</span></span> | <span data-ttu-id="78148-179">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="78148-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-180">90 seconds</span></span> | <span data-ttu-id="78148-181">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="78148-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="78148-182">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="78148-183">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="78148-184">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-184">Option</span></span> | <span data-ttu-id="78148-185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-185">Default Value</span></span> | <span data-ttu-id="78148-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="78148-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-187">5 seconds</span></span> | <span data-ttu-id="78148-188">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="78148-189">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="78148-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="78148-190">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="78148-191">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="78148-191">Configure client options</span></span>

<span data-ttu-id="78148-192">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="78148-193">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="78148-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="78148-194">registro</span><span class="sxs-lookup"><span data-stu-id="78148-194">Configure logging</span></span>

<span data-ttu-id="78148-195">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="78148-196">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="78148-197">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="78148-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-198">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="78148-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="78148-199">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="78148-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="78148-200">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="78148-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="78148-201">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="78148-202">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="78148-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="78148-203">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="78148-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="78148-204">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="78148-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="78148-205">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="78148-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="78148-206">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="78148-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="78148-207">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="78148-207">The following table lists the available log levels.</span></span> <span data-ttu-id="78148-208">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="78148-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="78148-209">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="78148-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="78148-210">Cadena</span><span class="sxs-lookup"><span data-stu-id="78148-210">String</span></span>                      | <span data-ttu-id="78148-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="78148-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="78148-212">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="78148-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="78148-213">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="78148-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="78148-214">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="78148-215">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="78148-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="78148-216">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="78148-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="78148-217">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="78148-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="78148-218">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="78148-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="78148-219">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="78148-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="78148-220">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="78148-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="78148-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="78148-221">Configure allowed transports</span></span>

<span data-ttu-id="78148-222">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="78148-223">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="78148-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="78148-224">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="78148-224">All transports are enabled by default.</span></span>

<span data-ttu-id="78148-225">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="78148-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="78148-226">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="78148-227">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="78148-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="78148-228">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="78148-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="78148-229">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="78148-230">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="78148-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="78148-231">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="78148-231">Configure bearer authentication</span></span>

<span data-ttu-id="78148-232">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="78148-233">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="78148-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="78148-234">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78148-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="78148-235">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="78148-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="78148-236">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="78148-237">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="78148-238">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="78148-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="78148-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="78148-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="78148-240">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="78148-241">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="78148-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="78148-242">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="78148-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-243">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-244">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-244">Option</span></span> | <span data-ttu-id="78148-245">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-245">Default value</span></span> | <span data-ttu-id="78148-246">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="78148-247">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-248">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-248">Timeout for server activity.</span></span> <span data-ttu-id="78148-249">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-250">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-251">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="78148-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-252">15 seconds</span></span> | <span data-ttu-id="78148-253">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-254">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-255">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-256">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-257">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-257">15 seconds</span></span> | <span data-ttu-id="78148-258">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-259">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-260">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="78148-261">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="78148-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="78148-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-263">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-263">Option</span></span> | <span data-ttu-id="78148-264">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-264">Default value</span></span> | <span data-ttu-id="78148-265">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="78148-266">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-267">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-267">Timeout for server activity.</span></span> <span data-ttu-id="78148-268">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="78148-269">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-270">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="78148-271">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-272">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-273">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-274">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-275">Java</span><span class="sxs-lookup"><span data-stu-id="78148-275">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-276">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-276">Option</span></span> | <span data-ttu-id="78148-277">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-277">Default value</span></span> | <span data-ttu-id="78148-278">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="78148-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="78148-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="78148-280">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-281">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-281">Timeout for server activity.</span></span> <span data-ttu-id="78148-282">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-283">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-284">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="78148-285">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-285">15 seconds</span></span> | <span data-ttu-id="78148-286">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-287">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-288">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-289">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="78148-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="78148-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="78148-291">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-292">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-293">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-294">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="78148-295">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-295">Configure additional options</span></span>

<span data-ttu-id="78148-296">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="78148-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-297">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-298">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="78148-298">.NET Option</span></span> |  <span data-ttu-id="78148-299">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-299">Default value</span></span> | <span data-ttu-id="78148-300">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="78148-301">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="78148-302">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-303">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-304">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="78148-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-305">Empty</span></span> | <span data-ttu-id="78148-306">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="78148-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="78148-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-307">Empty</span></span> | <span data-ttu-id="78148-308">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="78148-309">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-309">Empty</span></span> | <span data-ttu-id="78148-310">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="78148-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-311">5 seconds</span></span> | <span data-ttu-id="78148-312">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-312">WebSockets only.</span></span> <span data-ttu-id="78148-313">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="78148-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="78148-314">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="78148-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="78148-315">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-315">Empty</span></span> | <span data-ttu-id="78148-316">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="78148-317">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="78148-318">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="78148-319">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="78148-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="78148-320">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="78148-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="78148-321">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="78148-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="78148-322">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="78148-323">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="78148-324">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="78148-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="78148-325">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="78148-326">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="78148-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="78148-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-328">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-328">JavaScript Option</span></span> | <span data-ttu-id="78148-329">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-329">Default Value</span></span> | <span data-ttu-id="78148-330">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="78148-331">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="78148-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="78148-333">Diccionario de encabezados enviados con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="78148-334">El envío de encabezados en el explorador no funciona con WebSockets ni con el <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flujo.</span><span class="sxs-lookup"><span data-stu-id="78148-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="78148-335">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="78148-336">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-337">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-338">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="78148-339">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="78148-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="78148-340">Azure App Service usa cookie para las sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="78148-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="78148-341">Para obtener más información sobre CORS con SignalR , vea <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="78148-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-342">Java</span><span class="sxs-lookup"><span data-stu-id="78148-342">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-343">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="78148-343">Java Option</span></span> | <span data-ttu-id="78148-344">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-344">Default Value</span></span> | <span data-ttu-id="78148-345">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="78148-346">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="78148-347">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-348">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-349">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="78148-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="78148-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="78148-351">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-351">Empty</span></span> | <span data-ttu-id="78148-352">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="78148-353">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="78148-354">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="78148-355">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="78148-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="78148-356">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="78148-357">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="78148-358">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="78148-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="78148-359">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="78148-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="78148-360">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="78148-361">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="78148-362">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="78148-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="78148-363">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="78148-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="78148-364">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="78148-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="78148-365">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78148-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="78148-366">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="78148-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="78148-367">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="78148-368">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="78148-369">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="78148-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="78148-370">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="78148-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="78148-371">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-371">MessagePack serialization options</span></span>

<span data-ttu-id="78148-372">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="78148-373">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="78148-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-374">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="78148-375">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="78148-375">Configure server options</span></span>

<span data-ttu-id="78148-376">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="78148-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="78148-377">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-377">Option</span></span> | <span data-ttu-id="78148-378">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-378">Default Value</span></span> | <span data-ttu-id="78148-379">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="78148-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-380">30 seconds</span></span> | <span data-ttu-id="78148-381">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="78148-382">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="78148-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="78148-383">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="78148-384">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-384">15 seconds</span></span> | <span data-ttu-id="78148-385">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="78148-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="78148-386">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-387">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-388">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-388">15 seconds</span></span> | <span data-ttu-id="78148-389">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="78148-390">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="78148-391">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="78148-392">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="78148-392">All installed protocols</span></span> | <span data-ttu-id="78148-393">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="78148-393">Protocols supported by this hub.</span></span> <span data-ttu-id="78148-394">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="78148-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="78148-395">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="78148-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="78148-396">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="78148-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="78148-397">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="78148-398">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="78148-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-399">32 KB</span></span> | <span data-ttu-id="78148-400">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="78148-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="78148-401">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="78148-402">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="78148-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="78148-403">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="78148-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="78148-404">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="78148-405">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="78148-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="78148-406">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="78148-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="78148-407">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-407">Option</span></span> | <span data-ttu-id="78148-408">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-408">Default Value</span></span> | <span data-ttu-id="78148-409">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="78148-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-410">32 KB</span></span> | <span data-ttu-id="78148-411">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="78148-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="78148-412">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="78148-413">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="78148-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="78148-414">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="78148-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="78148-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-415">32 KB</span></span> | <span data-ttu-id="78148-416">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="78148-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="78148-417">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="78148-418">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="78148-418">All Transports are enabled.</span></span> | <span data-ttu-id="78148-419">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="78148-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="78148-420">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-420">See below.</span></span> | <span data-ttu-id="78148-421">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="78148-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="78148-422">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-422">See below.</span></span> | <span data-ttu-id="78148-423">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="78148-424">0</span><span class="sxs-lookup"><span data-stu-id="78148-424">0</span></span> | <span data-ttu-id="78148-425">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="78148-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="78148-426">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="78148-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="78148-427">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="78148-428">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-428">Option</span></span> | <span data-ttu-id="78148-429">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-429">Default Value</span></span> | <span data-ttu-id="78148-430">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="78148-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-431">90 seconds</span></span> | <span data-ttu-id="78148-432">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="78148-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="78148-433">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="78148-434">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="78148-435">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-435">Option</span></span> | <span data-ttu-id="78148-436">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-436">Default Value</span></span> | <span data-ttu-id="78148-437">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="78148-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-438">5 seconds</span></span> | <span data-ttu-id="78148-439">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="78148-440">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="78148-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="78148-441">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="78148-442">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="78148-442">Configure client options</span></span>

<span data-ttu-id="78148-443">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="78148-444">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="78148-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="78148-445">registro</span><span class="sxs-lookup"><span data-stu-id="78148-445">Configure logging</span></span>

<span data-ttu-id="78148-446">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="78148-447">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="78148-448">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="78148-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-449">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="78148-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="78148-450">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="78148-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="78148-451">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="78148-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="78148-452">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="78148-453">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="78148-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="78148-454">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="78148-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="78148-455">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="78148-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="78148-456">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="78148-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="78148-457">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="78148-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="78148-458">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="78148-458">The following table lists the available log levels.</span></span> <span data-ttu-id="78148-459">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="78148-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="78148-460">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="78148-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="78148-461">Cadena</span><span class="sxs-lookup"><span data-stu-id="78148-461">String</span></span>                      | <span data-ttu-id="78148-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="78148-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="78148-463">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="78148-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="78148-464">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="78148-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="78148-465">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="78148-466">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="78148-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="78148-467">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="78148-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="78148-468">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="78148-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="78148-469">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="78148-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="78148-470">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="78148-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="78148-471">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="78148-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="78148-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="78148-472">Configure allowed transports</span></span>

<span data-ttu-id="78148-473">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="78148-474">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="78148-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="78148-475">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="78148-475">All transports are enabled by default.</span></span>

<span data-ttu-id="78148-476">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="78148-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="78148-477">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="78148-478">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="78148-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="78148-479">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="78148-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="78148-480">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="78148-481">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="78148-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="78148-482">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="78148-482">Configure bearer authentication</span></span>

<span data-ttu-id="78148-483">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="78148-484">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="78148-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="78148-485">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78148-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="78148-486">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="78148-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="78148-487">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="78148-488">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="78148-489">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="78148-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="78148-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="78148-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="78148-491">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="78148-492">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="78148-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="78148-493">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="78148-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-494">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-495">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-495">Option</span></span> | <span data-ttu-id="78148-496">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-496">Default value</span></span> | <span data-ttu-id="78148-497">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="78148-498">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-499">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-499">Timeout for server activity.</span></span> <span data-ttu-id="78148-500">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-501">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-502">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="78148-503">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-503">15 seconds</span></span> | <span data-ttu-id="78148-504">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-505">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-506">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-507">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-508">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-508">15 seconds</span></span> | <span data-ttu-id="78148-509">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-510">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-511">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="78148-512">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="78148-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="78148-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-514">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-514">Option</span></span> | <span data-ttu-id="78148-515">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-515">Default value</span></span> | <span data-ttu-id="78148-516">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="78148-517">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-518">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-518">Timeout for server activity.</span></span> <span data-ttu-id="78148-519">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="78148-520">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-521">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="78148-522">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-523">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-524">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-525">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-526">Java</span><span class="sxs-lookup"><span data-stu-id="78148-526">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-527">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-527">Option</span></span> | <span data-ttu-id="78148-528">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-528">Default value</span></span> | <span data-ttu-id="78148-529">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="78148-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="78148-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="78148-531">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-532">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-532">Timeout for server activity.</span></span> <span data-ttu-id="78148-533">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-534">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-535">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="78148-536">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-536">15 seconds</span></span> | <span data-ttu-id="78148-537">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-538">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-539">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-540">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="78148-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="78148-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="78148-542">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-543">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-544">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-545">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="78148-546">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-546">Configure additional options</span></span>

<span data-ttu-id="78148-547">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="78148-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-548">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-549">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="78148-549">.NET Option</span></span> |  <span data-ttu-id="78148-550">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-550">Default value</span></span> | <span data-ttu-id="78148-551">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="78148-552">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="78148-553">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-554">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-555">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="78148-556">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-556">Empty</span></span> | <span data-ttu-id="78148-557">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="78148-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="78148-558">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-558">Empty</span></span> | <span data-ttu-id="78148-559">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="78148-560">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-560">Empty</span></span> | <span data-ttu-id="78148-561">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="78148-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-562">5 seconds</span></span> | <span data-ttu-id="78148-563">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-563">WebSockets only.</span></span> <span data-ttu-id="78148-564">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="78148-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="78148-565">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="78148-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="78148-566">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-566">Empty</span></span> | <span data-ttu-id="78148-567">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="78148-568">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="78148-569">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="78148-570">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="78148-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="78148-571">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="78148-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="78148-572">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="78148-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="78148-573">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="78148-574">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="78148-575">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="78148-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="78148-576">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="78148-577">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="78148-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="78148-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-579">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-579">JavaScript Option</span></span> | <span data-ttu-id="78148-580">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-580">Default Value</span></span> | <span data-ttu-id="78148-581">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="78148-582">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="78148-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="78148-584">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="78148-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-587">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-588">Java</span><span class="sxs-lookup"><span data-stu-id="78148-588">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-589">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="78148-589">Java Option</span></span> | <span data-ttu-id="78148-590">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-590">Default Value</span></span> | <span data-ttu-id="78148-591">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="78148-592">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="78148-593">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-594">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-595">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="78148-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="78148-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="78148-597">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-597">Empty</span></span> | <span data-ttu-id="78148-598">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="78148-599">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="78148-600">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="78148-601">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="78148-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="78148-602">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="78148-603">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="78148-604">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="78148-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="78148-605">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="78148-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="78148-606">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="78148-607">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="78148-608">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="78148-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="78148-609">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="78148-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="78148-610">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="78148-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="78148-611">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78148-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="78148-612">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="78148-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="78148-613">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="78148-614">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="78148-615">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="78148-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="78148-616">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="78148-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="78148-617">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-617">MessagePack serialization options</span></span>

<span data-ttu-id="78148-618">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="78148-619">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="78148-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-620">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="78148-621">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="78148-621">Configure server options</span></span>

<span data-ttu-id="78148-622">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="78148-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="78148-623">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-623">Option</span></span> | <span data-ttu-id="78148-624">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-624">Default Value</span></span> | <span data-ttu-id="78148-625">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="78148-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-626">30 seconds</span></span> | <span data-ttu-id="78148-627">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="78148-628">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="78148-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="78148-629">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="78148-630">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-630">15 seconds</span></span> | <span data-ttu-id="78148-631">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="78148-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="78148-632">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-633">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-634">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-634">15 seconds</span></span> | <span data-ttu-id="78148-635">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="78148-636">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="78148-637">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="78148-638">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="78148-638">All installed protocols</span></span> | <span data-ttu-id="78148-639">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="78148-639">Protocols supported by this hub.</span></span> <span data-ttu-id="78148-640">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="78148-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="78148-641">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="78148-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="78148-642">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="78148-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="78148-643">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="78148-644">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="78148-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-645">32 KB</span></span> | <span data-ttu-id="78148-646">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="78148-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="78148-647">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="78148-648">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="78148-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="78148-649">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="78148-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="78148-650">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="78148-651">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="78148-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="78148-652">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="78148-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="78148-653">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-653">Option</span></span> | <span data-ttu-id="78148-654">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-654">Default Value</span></span> | <span data-ttu-id="78148-655">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="78148-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-656">32 KB</span></span> | <span data-ttu-id="78148-657">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="78148-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="78148-658">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="78148-659">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="78148-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="78148-660">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="78148-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="78148-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-661">32 KB</span></span> | <span data-ttu-id="78148-662">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="78148-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="78148-663">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="78148-664">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="78148-664">All Transports are enabled.</span></span> | <span data-ttu-id="78148-665">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="78148-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="78148-666">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-666">See below.</span></span> | <span data-ttu-id="78148-667">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="78148-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="78148-668">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-668">See below.</span></span> | <span data-ttu-id="78148-669">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="78148-670">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="78148-671">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-671">Option</span></span> | <span data-ttu-id="78148-672">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-672">Default Value</span></span> | <span data-ttu-id="78148-673">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="78148-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-674">90 seconds</span></span> | <span data-ttu-id="78148-675">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="78148-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="78148-676">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="78148-677">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="78148-678">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-678">Option</span></span> | <span data-ttu-id="78148-679">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-679">Default Value</span></span> | <span data-ttu-id="78148-680">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="78148-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-681">5 seconds</span></span> | <span data-ttu-id="78148-682">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="78148-683">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="78148-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="78148-684">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="78148-685">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="78148-685">Configure client options</span></span>

<span data-ttu-id="78148-686">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="78148-687">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="78148-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="78148-688">registro</span><span class="sxs-lookup"><span data-stu-id="78148-688">Configure logging</span></span>

<span data-ttu-id="78148-689">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="78148-690">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="78148-691">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="78148-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-692">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="78148-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="78148-693">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="78148-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="78148-694">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="78148-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="78148-695">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="78148-696">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="78148-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="78148-697">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="78148-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="78148-698">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="78148-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="78148-699">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="78148-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="78148-700">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="78148-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="78148-701">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="78148-701">The following table lists the available log levels.</span></span> <span data-ttu-id="78148-702">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="78148-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="78148-703">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="78148-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="78148-704">Cadena</span><span class="sxs-lookup"><span data-stu-id="78148-704">String</span></span>                      | <span data-ttu-id="78148-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="78148-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="78148-706">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="78148-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="78148-707">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="78148-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="78148-708">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="78148-709">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="78148-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="78148-710">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="78148-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="78148-711">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="78148-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="78148-712">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="78148-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="78148-713">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="78148-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="78148-714">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="78148-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="78148-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="78148-715">Configure allowed transports</span></span>

<span data-ttu-id="78148-716">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="78148-717">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="78148-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="78148-718">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="78148-718">All transports are enabled by default.</span></span>

<span data-ttu-id="78148-719">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="78148-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="78148-720">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="78148-721">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="78148-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="78148-722">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="78148-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="78148-723">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="78148-724">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="78148-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="78148-725">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="78148-725">Configure bearer authentication</span></span>

<span data-ttu-id="78148-726">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="78148-727">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="78148-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="78148-728">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78148-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="78148-729">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="78148-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="78148-730">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="78148-731">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="78148-732">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="78148-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="78148-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="78148-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="78148-734">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="78148-735">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="78148-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="78148-736">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="78148-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-737">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-738">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-738">Option</span></span> | <span data-ttu-id="78148-739">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-739">Default value</span></span> | <span data-ttu-id="78148-740">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="78148-741">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-742">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-742">Timeout for server activity.</span></span> <span data-ttu-id="78148-743">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-744">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-745">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="78148-746">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-746">15 seconds</span></span> | <span data-ttu-id="78148-747">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-748">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-749">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-750">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-751">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-751">15 seconds</span></span> | <span data-ttu-id="78148-752">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-753">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-754">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="78148-755">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="78148-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="78148-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-757">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-757">Option</span></span> | <span data-ttu-id="78148-758">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-758">Default value</span></span> | <span data-ttu-id="78148-759">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="78148-760">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-761">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-761">Timeout for server activity.</span></span> <span data-ttu-id="78148-762">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="78148-763">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-764">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="78148-765">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-766">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-767">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-768">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-769">Java</span><span class="sxs-lookup"><span data-stu-id="78148-769">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-770">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-770">Option</span></span> | <span data-ttu-id="78148-771">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-771">Default value</span></span> | <span data-ttu-id="78148-772">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="78148-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="78148-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="78148-774">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-775">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-775">Timeout for server activity.</span></span> <span data-ttu-id="78148-776">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-777">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-778">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="78148-779">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-779">15 seconds</span></span> | <span data-ttu-id="78148-780">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-781">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-782">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-783">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="78148-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="78148-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="78148-785">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-786">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-787">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-788">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="78148-789">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-789">Configure additional options</span></span>

<span data-ttu-id="78148-790">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="78148-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-791">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-792">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="78148-792">.NET Option</span></span> |  <span data-ttu-id="78148-793">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-793">Default value</span></span> | <span data-ttu-id="78148-794">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="78148-795">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="78148-796">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-797">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-798">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="78148-799">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-799">Empty</span></span> | <span data-ttu-id="78148-800">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="78148-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="78148-801">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-801">Empty</span></span> | <span data-ttu-id="78148-802">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="78148-803">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-803">Empty</span></span> | <span data-ttu-id="78148-804">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="78148-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-805">5 seconds</span></span> | <span data-ttu-id="78148-806">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-806">WebSockets only.</span></span> <span data-ttu-id="78148-807">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="78148-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="78148-808">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="78148-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="78148-809">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-809">Empty</span></span> | <span data-ttu-id="78148-810">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="78148-811">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="78148-812">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="78148-813">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="78148-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="78148-814">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="78148-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="78148-815">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="78148-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="78148-816">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="78148-817">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="78148-818">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="78148-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="78148-819">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="78148-820">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="78148-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="78148-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-822">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-822">JavaScript Option</span></span> | <span data-ttu-id="78148-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-823">Default Value</span></span> | <span data-ttu-id="78148-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="78148-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="78148-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="78148-827">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="78148-828">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-829">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-830">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-831">Java</span><span class="sxs-lookup"><span data-stu-id="78148-831">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-832">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="78148-832">Java Option</span></span> | <span data-ttu-id="78148-833">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-833">Default Value</span></span> | <span data-ttu-id="78148-834">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="78148-835">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="78148-836">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-837">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-838">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="78148-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="78148-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="78148-840">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-840">Empty</span></span> | <span data-ttu-id="78148-841">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="78148-842">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="78148-843">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="78148-844">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="78148-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="78148-845">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="78148-846">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="78148-847">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="78148-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="78148-848">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="78148-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="78148-849">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="78148-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="78148-850">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="78148-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="78148-851">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="78148-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="78148-852">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="78148-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="78148-853">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78148-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="78148-854">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="78148-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="78148-855">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="78148-856">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="78148-857">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-857">MessagePack serialization options</span></span>

<span data-ttu-id="78148-858">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="78148-859">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="78148-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-860">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="78148-861">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="78148-861">Configure server options</span></span>

<span data-ttu-id="78148-862">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="78148-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="78148-863">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-863">Option</span></span> | <span data-ttu-id="78148-864">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-864">Default Value</span></span> | <span data-ttu-id="78148-865">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="78148-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-866">30 seconds</span></span> | <span data-ttu-id="78148-867">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="78148-868">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="78148-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="78148-869">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="78148-870">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-870">15 seconds</span></span> | <span data-ttu-id="78148-871">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="78148-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="78148-872">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-873">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-874">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-874">15 seconds</span></span> | <span data-ttu-id="78148-875">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="78148-876">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="78148-877">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="78148-878">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="78148-878">All installed protocols</span></span> | <span data-ttu-id="78148-879">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="78148-879">Protocols supported by this hub.</span></span> <span data-ttu-id="78148-880">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="78148-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="78148-881">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="78148-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="78148-882">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="78148-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="78148-883">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="78148-884">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="78148-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="78148-885">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="78148-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="78148-886">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="78148-887">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="78148-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="78148-888">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="78148-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="78148-889">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-889">Option</span></span> | <span data-ttu-id="78148-890">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-890">Default Value</span></span> | <span data-ttu-id="78148-891">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="78148-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-892">32 KB</span></span> | <span data-ttu-id="78148-893">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="78148-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="78148-894">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="78148-895">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="78148-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="78148-896">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="78148-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="78148-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-897">32 KB</span></span> | <span data-ttu-id="78148-898">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="78148-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="78148-899">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="78148-900">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="78148-900">All Transports are enabled.</span></span> | <span data-ttu-id="78148-901">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="78148-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="78148-902">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-902">See below.</span></span> | <span data-ttu-id="78148-903">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="78148-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="78148-904">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-904">See below.</span></span> | <span data-ttu-id="78148-905">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="78148-906">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="78148-907">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-907">Option</span></span> | <span data-ttu-id="78148-908">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-908">Default Value</span></span> | <span data-ttu-id="78148-909">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="78148-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-910">90 seconds</span></span> | <span data-ttu-id="78148-911">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="78148-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="78148-912">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="78148-913">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="78148-914">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-914">Option</span></span> | <span data-ttu-id="78148-915">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-915">Default Value</span></span> | <span data-ttu-id="78148-916">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="78148-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-917">5 seconds</span></span> | <span data-ttu-id="78148-918">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="78148-919">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="78148-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="78148-920">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="78148-921">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="78148-921">Configure client options</span></span>

<span data-ttu-id="78148-922">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="78148-923">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="78148-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="78148-924">registro</span><span class="sxs-lookup"><span data-stu-id="78148-924">Configure logging</span></span>

<span data-ttu-id="78148-925">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="78148-926">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="78148-927">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="78148-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-928">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="78148-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="78148-929">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="78148-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="78148-930">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="78148-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="78148-931">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="78148-932">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="78148-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="78148-933">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="78148-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="78148-934">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="78148-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="78148-935">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="78148-936">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="78148-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="78148-937">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="78148-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="78148-938">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="78148-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="78148-939">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="78148-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="78148-940">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="78148-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="78148-941">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="78148-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="78148-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="78148-942">Configure allowed transports</span></span>

<span data-ttu-id="78148-943">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="78148-944">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="78148-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="78148-945">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="78148-945">All transports are enabled by default.</span></span>

<span data-ttu-id="78148-946">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="78148-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="78148-947">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="78148-948">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="78148-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="78148-949">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="78148-949">Configure bearer authentication</span></span>

<span data-ttu-id="78148-950">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="78148-951">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="78148-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="78148-952">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78148-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="78148-953">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="78148-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="78148-954">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="78148-955">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="78148-956">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="78148-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="78148-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="78148-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="78148-958">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="78148-959">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="78148-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="78148-960">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="78148-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-961">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-962">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-962">Option</span></span> | <span data-ttu-id="78148-963">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-963">Default value</span></span> | <span data-ttu-id="78148-964">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="78148-965">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-966">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-966">Timeout for server activity.</span></span> <span data-ttu-id="78148-967">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-968">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-969">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="78148-970">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-970">15 seconds</span></span> | <span data-ttu-id="78148-971">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-972">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-973">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-974">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-975">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-975">15 seconds</span></span> | <span data-ttu-id="78148-976">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-977">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-978">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="78148-979">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="78148-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="78148-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-981">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-981">Option</span></span> | <span data-ttu-id="78148-982">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-982">Default value</span></span> | <span data-ttu-id="78148-983">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="78148-984">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-985">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-985">Timeout for server activity.</span></span> <span data-ttu-id="78148-986">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="78148-987">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-988">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="78148-989">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-990">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-991">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-992">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-993">Java</span><span class="sxs-lookup"><span data-stu-id="78148-993">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-994">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-994">Option</span></span> | <span data-ttu-id="78148-995">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-995">Default value</span></span> | <span data-ttu-id="78148-996">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="78148-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="78148-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="78148-998">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-999">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-999">Timeout for server activity.</span></span> <span data-ttu-id="78148-1000">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-1001">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-1002">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="78148-1003">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1003">15 seconds</span></span> | <span data-ttu-id="78148-1004">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-1005">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-1006">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-1007">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="78148-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="78148-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="78148-1009">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="78148-1010">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="78148-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="78148-1011">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="78148-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="78148-1012">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="78148-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="78148-1013">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-1013">Configure additional options</span></span>

<span data-ttu-id="78148-1014">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="78148-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-1016">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="78148-1016">.NET Option</span></span> |  <span data-ttu-id="78148-1017">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1017">Default value</span></span> | <span data-ttu-id="78148-1018">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="78148-1019">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="78148-1020">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1021">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1022">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="78148-1023">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1023">Empty</span></span> | <span data-ttu-id="78148-1024">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="78148-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="78148-1025">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1025">Empty</span></span> | <span data-ttu-id="78148-1026">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="78148-1027">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1027">Empty</span></span> | <span data-ttu-id="78148-1028">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="78148-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1029">5 seconds</span></span> | <span data-ttu-id="78148-1030">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-1030">WebSockets only.</span></span> <span data-ttu-id="78148-1031">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="78148-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="78148-1032">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="78148-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="78148-1033">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1033">Empty</span></span> | <span data-ttu-id="78148-1034">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="78148-1035">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="78148-1036">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="78148-1037">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="78148-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="78148-1038">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="78148-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="78148-1039">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="78148-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="78148-1040">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="78148-1041">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="78148-1042">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="78148-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="78148-1043">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="78148-1044">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="78148-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="78148-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-1046">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-1046">JavaScript Option</span></span> | <span data-ttu-id="78148-1047">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1047">Default Value</span></span> | <span data-ttu-id="78148-1048">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="78148-1049">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="78148-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="78148-1051">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="78148-1052">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1053">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1054">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-1055">Java</span><span class="sxs-lookup"><span data-stu-id="78148-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-1056">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="78148-1056">Java Option</span></span> | <span data-ttu-id="78148-1057">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1057">Default Value</span></span> | <span data-ttu-id="78148-1058">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="78148-1059">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="78148-1060">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1061">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1062">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="78148-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="78148-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="78148-1064">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1064">Empty</span></span> | <span data-ttu-id="78148-1065">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="78148-1066">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="78148-1067">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="78148-1068">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="78148-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="78148-1069">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="78148-1070">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="78148-1071">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="78148-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="78148-1072">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="78148-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="78148-1073">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="78148-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="78148-1074">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="78148-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="78148-1075">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="78148-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="78148-1076">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="78148-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="78148-1077">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78148-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="78148-1078">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="78148-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="78148-1079">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="78148-1080">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="78148-1081">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="78148-1081">MessagePack serialization options</span></span>

<span data-ttu-id="78148-1082">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="78148-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="78148-1083">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="78148-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-1084">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78148-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="78148-1085">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="78148-1085">Configure server options</span></span>

<span data-ttu-id="78148-1086">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="78148-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="78148-1087">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1087">Option</span></span> | <span data-ttu-id="78148-1088">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1088">Default Value</span></span> | <span data-ttu-id="78148-1089">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="78148-1090">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1090">15 seconds</span></span> | <span data-ttu-id="78148-1091">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="78148-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="78148-1092">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-1093">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="78148-1094">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1094">15 seconds</span></span> | <span data-ttu-id="78148-1095">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="78148-1096">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="78148-1097">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="78148-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="78148-1098">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="78148-1098">All installed protocols</span></span> | <span data-ttu-id="78148-1099">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="78148-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="78148-1100">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="78148-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="78148-1101">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="78148-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="78148-1102">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="78148-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="78148-1103">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78148-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="78148-1104">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="78148-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="78148-1105">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="78148-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="78148-1106">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="78148-1107">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="78148-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="78148-1108">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="78148-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="78148-1109">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1109">Option</span></span> | <span data-ttu-id="78148-1110">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1110">Default Value</span></span> | <span data-ttu-id="78148-1111">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="78148-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-1112">32 KB</span></span> | <span data-ttu-id="78148-1113">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="78148-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="78148-1114">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="78148-1115">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="78148-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="78148-1116">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="78148-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="78148-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="78148-1117">32 KB</span></span> | <span data-ttu-id="78148-1118">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="78148-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="78148-1119">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="78148-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="78148-1120">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="78148-1120">All Transports are enabled.</span></span> | <span data-ttu-id="78148-1121">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="78148-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="78148-1122">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-1122">See below.</span></span> | <span data-ttu-id="78148-1123">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="78148-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="78148-1124">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="78148-1124">See below.</span></span> | <span data-ttu-id="78148-1125">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="78148-1126">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="78148-1127">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1127">Option</span></span> | <span data-ttu-id="78148-1128">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1128">Default Value</span></span> | <span data-ttu-id="78148-1129">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="78148-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1130">90 seconds</span></span> | <span data-ttu-id="78148-1131">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="78148-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="78148-1132">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="78148-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="78148-1133">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="78148-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="78148-1134">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1134">Option</span></span> | <span data-ttu-id="78148-1135">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1135">Default Value</span></span> | <span data-ttu-id="78148-1136">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="78148-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1137">5 seconds</span></span> | <span data-ttu-id="78148-1138">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="78148-1139">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="78148-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="78148-1140">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="78148-1141">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="78148-1141">Configure client options</span></span>

<span data-ttu-id="78148-1142">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="78148-1143">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="78148-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="78148-1144">registro</span><span class="sxs-lookup"><span data-stu-id="78148-1144">Configure logging</span></span>

<span data-ttu-id="78148-1145">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="78148-1146">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="78148-1147">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="78148-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="78148-1148">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="78148-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="78148-1149">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="78148-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="78148-1150">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="78148-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="78148-1151">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="78148-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="78148-1152">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="78148-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="78148-1153">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="78148-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="78148-1154">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="78148-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="78148-1155">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="78148-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="78148-1156">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="78148-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="78148-1157">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="78148-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="78148-1158">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="78148-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="78148-1159">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="78148-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="78148-1160">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="78148-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="78148-1161">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="78148-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="78148-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="78148-1162">Configure allowed transports</span></span>

<span data-ttu-id="78148-1163">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="78148-1164">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="78148-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="78148-1165">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="78148-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="78148-1166">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="78148-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="78148-1167">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="78148-1168">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="78148-1168">Configure bearer authentication</span></span>

<span data-ttu-id="78148-1169">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="78148-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="78148-1170">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="78148-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="78148-1171">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78148-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="78148-1172">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="78148-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="78148-1173">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="78148-1174">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="78148-1175">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="78148-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="78148-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="78148-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="78148-1177">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="78148-1178">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="78148-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="78148-1179">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="78148-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-1181">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1181">Option</span></span> | <span data-ttu-id="78148-1182">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1182">Default value</span></span> | <span data-ttu-id="78148-1183">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="78148-1184">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-1185">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1185">Timeout for server activity.</span></span> <span data-ttu-id="78148-1186">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-1187">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-1188">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="78148-1189">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1189">15 seconds</span></span> | <span data-ttu-id="78148-1190">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-1191">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="78148-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="78148-1192">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-1193">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="78148-1194">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="78148-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="78148-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-1196">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1196">Option</span></span> | <span data-ttu-id="78148-1197">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1197">Default value</span></span> | <span data-ttu-id="78148-1198">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="78148-1199">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-1200">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1200">Timeout for server activity.</span></span> <span data-ttu-id="78148-1201">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="78148-1202">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-1203">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-1204">Java</span><span class="sxs-lookup"><span data-stu-id="78148-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-1205">Opción</span><span class="sxs-lookup"><span data-stu-id="78148-1205">Option</span></span> | <span data-ttu-id="78148-1206">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1206">Default value</span></span> | <span data-ttu-id="78148-1207">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="78148-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="78148-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="78148-1209">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="78148-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="78148-1210">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1210">Timeout for server activity.</span></span> <span data-ttu-id="78148-1211">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-1212">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="78148-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="78148-1213">El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="78148-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="78148-1214">15 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1214">15 seconds</span></span> | <span data-ttu-id="78148-1215">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="78148-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="78148-1216">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="78148-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="78148-1217">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="78148-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="78148-1218">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="78148-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="78148-1219">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-1219">Configure additional options</span></span>

<span data-ttu-id="78148-1220">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="78148-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="78148-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="78148-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="78148-1222">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="78148-1222">.NET Option</span></span> |  <span data-ttu-id="78148-1223">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1223">Default value</span></span> | <span data-ttu-id="78148-1224">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="78148-1225">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="78148-1226">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1227">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1228">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="78148-1229">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1229">Empty</span></span> | <span data-ttu-id="78148-1230">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="78148-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="78148-1231">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1231">Empty</span></span> | <span data-ttu-id="78148-1232">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="78148-1233">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1233">Empty</span></span> | <span data-ttu-id="78148-1234">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="78148-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="78148-1235">5 seconds</span></span> | <span data-ttu-id="78148-1236">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-1236">WebSockets only.</span></span> <span data-ttu-id="78148-1237">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="78148-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="78148-1238">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="78148-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="78148-1239">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1239">Empty</span></span> | <span data-ttu-id="78148-1240">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="78148-1241">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="78148-1242">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="78148-1243">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="78148-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="78148-1244">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="78148-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="78148-1245">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="78148-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="78148-1246">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="78148-1247">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="78148-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="78148-1248">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="78148-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="78148-1249">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="78148-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="78148-1250">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="78148-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="78148-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="78148-1252">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="78148-1252">JavaScript Option</span></span> | <span data-ttu-id="78148-1253">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1253">Default Value</span></span> | <span data-ttu-id="78148-1254">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="78148-1255">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="78148-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="78148-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="78148-1257">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="78148-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="78148-1258">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1259">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1260">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="78148-1261">Java</span><span class="sxs-lookup"><span data-stu-id="78148-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="78148-1262">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="78148-1262">Java Option</span></span> | <span data-ttu-id="78148-1263">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="78148-1263">Default Value</span></span> | <span data-ttu-id="78148-1264">Descripción</span><span class="sxs-lookup"><span data-stu-id="78148-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="78148-1265">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="78148-1266">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="78148-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="78148-1267">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="78148-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="78148-1268">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="78148-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="78148-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="78148-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="78148-1270">Vacío</span><span class="sxs-lookup"><span data-stu-id="78148-1270">Empty</span></span> | <span data-ttu-id="78148-1271">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="78148-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="78148-1272">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="78148-1273">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="78148-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="78148-1274">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="78148-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="78148-1275">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78148-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
