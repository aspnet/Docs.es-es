---
title: Configuración de SignalR en ASP.NET Core
author: bradygaster
description: Aprenda a configurar ASP.NET Core SignalR aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393878"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="c93b2-103">Configuración de SignalR en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c93b2-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c93b2-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-105">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c93b2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c93b2-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="c93b2-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c93b2-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c93b2-108">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c93b2-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="c93b2-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c93b2-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="c93b2-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c93b2-111">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c93b2-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c93b2-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c93b2-113">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c93b2-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c93b2-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c93b2-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c93b2-116">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="c93b2-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c93b2-117">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c93b2-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c93b2-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-118">MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c93b2-120">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c93b2-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="c93b2-122">Configure server options</span></span>

<span data-ttu-id="c93b2-123">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="c93b2-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c93b2-124">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-124">Option</span></span> | <span data-ttu-id="c93b2-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-125">Default Value</span></span> | <span data-ttu-id="c93b2-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c93b2-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-127">30 seconds</span></span> | <span data-ttu-id="c93b2-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c93b2-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c93b2-130">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c93b2-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-131">15 seconds</span></span> | <span data-ttu-id="c93b2-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="c93b2-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c93b2-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-134">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-135">15 seconds</span></span> | <span data-ttu-id="c93b2-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c93b2-137">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c93b2-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c93b2-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="c93b2-139">All installed protocols</span></span> | <span data-ttu-id="c93b2-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-140">Protocols supported by this hub.</span></span> <span data-ttu-id="c93b2-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="c93b2-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c93b2-142">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c93b2-143">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="c93b2-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c93b2-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c93b2-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c93b2-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-146">32 KB</span></span> | <span data-ttu-id="c93b2-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="c93b2-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="c93b2-148">1</span><span class="sxs-lookup"><span data-stu-id="c93b2-148">1</span></span> | <span data-ttu-id="c93b2-149">Número máximo de métodos de concentrador a los que cada cliente puede llamar en paralelo antes de poner en cola.</span><span class="sxs-lookup"><span data-stu-id="c93b2-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="c93b2-150">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c93b2-151">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c93b2-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c93b2-152">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="c93b2-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c93b2-153">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c93b2-154">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c93b2-155">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="c93b2-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c93b2-156">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-156">Option</span></span> | <span data-ttu-id="c93b2-157">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-157">Default Value</span></span> | <span data-ttu-id="c93b2-158">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c93b2-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-159">32 KB</span></span> | <span data-ttu-id="c93b2-160">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="c93b2-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c93b2-161">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c93b2-162">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="c93b2-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c93b2-163">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c93b2-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-164">32 KB</span></span> | <span data-ttu-id="c93b2-165">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c93b2-166">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c93b2-167">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-167">All Transports are enabled.</span></span> | <span data-ttu-id="c93b2-168">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="c93b2-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c93b2-169">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-169">See below.</span></span> | <span data-ttu-id="c93b2-170">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c93b2-171">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-171">See below.</span></span> | <span data-ttu-id="c93b2-172">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c93b2-173">0</span><span class="sxs-lookup"><span data-stu-id="c93b2-173">0</span></span> | <span data-ttu-id="c93b2-174">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="c93b2-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c93b2-175">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="c93b2-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c93b2-176">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c93b2-177">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-177">Option</span></span> | <span data-ttu-id="c93b2-178">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-178">Default Value</span></span> | <span data-ttu-id="c93b2-179">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c93b2-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-180">90 seconds</span></span> | <span data-ttu-id="c93b2-181">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c93b2-182">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c93b2-183">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c93b2-184">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-184">Option</span></span> | <span data-ttu-id="c93b2-185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-185">Default Value</span></span> | <span data-ttu-id="c93b2-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c93b2-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-187">5 seconds</span></span> | <span data-ttu-id="c93b2-188">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c93b2-189">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c93b2-190">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c93b2-191">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="c93b2-191">Configure client options</span></span>

<span data-ttu-id="c93b2-192">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c93b2-193">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="c93b2-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c93b2-194">registro</span><span class="sxs-lookup"><span data-stu-id="c93b2-194">Configure logging</span></span>

<span data-ttu-id="c93b2-195">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c93b2-196">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c93b2-197">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c93b2-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-198">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="c93b2-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c93b2-199">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c93b2-200">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="c93b2-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c93b2-201">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c93b2-202">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="c93b2-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c93b2-203">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="c93b2-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c93b2-204">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c93b2-205">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c93b2-206">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="c93b2-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c93b2-207">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-207">The following table lists the available log levels.</span></span> <span data-ttu-id="c93b2-208">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="c93b2-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c93b2-209">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c93b2-210">String</span><span class="sxs-lookup"><span data-stu-id="c93b2-210">String</span></span>                      | <span data-ttu-id="c93b2-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c93b2-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c93b2-212">`info`**o bien**`information`</span><span class="sxs-lookup"><span data-stu-id="c93b2-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c93b2-213">`warn`**o bien**`warning`</span><span class="sxs-lookup"><span data-stu-id="c93b2-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c93b2-214">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c93b2-215">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c93b2-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c93b2-216">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c93b2-217">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="c93b2-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c93b2-218">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="c93b2-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c93b2-219">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="c93b2-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c93b2-220">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c93b2-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="c93b2-221">Configure allowed transports</span></span>

<span data-ttu-id="c93b2-222">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c93b2-223">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c93b2-224">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c93b2-224">All transports are enabled by default.</span></span>

<span data-ttu-id="c93b2-225">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="c93b2-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c93b2-226">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c93b2-227">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="c93b2-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c93b2-228">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c93b2-229">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c93b2-230">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="c93b2-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c93b2-231">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="c93b2-231">Configure bearer authentication</span></span>

<span data-ttu-id="c93b2-232">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c93b2-233">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c93b2-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c93b2-234">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c93b2-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c93b2-235">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c93b2-236">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c93b2-237">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c93b2-238">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c93b2-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c93b2-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="c93b2-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c93b2-240">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c93b2-241">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="c93b2-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c93b2-242">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-243">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-244">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-244">Option</span></span> | <span data-ttu-id="c93b2-245">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-245">Default value</span></span> | <span data-ttu-id="c93b2-246">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c93b2-247">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-248">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-248">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-249">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-250">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-251">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-252">15 seconds</span></span> | <span data-ttu-id="c93b2-253">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-254">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-255">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-256">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-257">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-257">15 seconds</span></span> | <span data-ttu-id="c93b2-258">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-259">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-260">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c93b2-261">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="c93b2-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c93b2-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-263">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-263">Option</span></span> | <span data-ttu-id="c93b2-264">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-264">Default value</span></span> | <span data-ttu-id="c93b2-265">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c93b2-266">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-267">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-267">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-268">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c93b2-269">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-270">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c93b2-271">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-272">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-273">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-274">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-275">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-275">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-276">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-276">Option</span></span> | <span data-ttu-id="c93b2-277">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-277">Default value</span></span> | <span data-ttu-id="c93b2-278">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c93b2-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c93b2-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c93b2-280">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-281">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-281">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-282">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-283">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-284">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c93b2-285">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-285">15 seconds</span></span> | <span data-ttu-id="c93b2-286">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-287">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-288">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-289">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c93b2-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c93b2-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c93b2-291">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-292">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-293">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-294">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c93b2-295">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-295">Configure additional options</span></span>

<span data-ttu-id="c93b2-296">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="c93b2-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-297">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-298">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-298">.NET Option</span></span> |  <span data-ttu-id="c93b2-299">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-299">Default value</span></span> | <span data-ttu-id="c93b2-300">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c93b2-301">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c93b2-302">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-303">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-304">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c93b2-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-305">Empty</span></span> | <span data-ttu-id="c93b2-306">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c93b2-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-307">Empty</span></span> | <span data-ttu-id="c93b2-308">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c93b2-309">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-309">Empty</span></span> | <span data-ttu-id="c93b2-310">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c93b2-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-311">5 seconds</span></span> | <span data-ttu-id="c93b2-312">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-312">WebSockets only.</span></span> <span data-ttu-id="c93b2-313">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="c93b2-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c93b2-314">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c93b2-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c93b2-315">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-315">Empty</span></span> | <span data-ttu-id="c93b2-316">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c93b2-317">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c93b2-318">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="c93b2-319">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c93b2-320">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c93b2-321">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="c93b2-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c93b2-322">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c93b2-323">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c93b2-324">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c93b2-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c93b2-325">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c93b2-326">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="c93b2-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c93b2-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-328">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-328">JavaScript Option</span></span> | <span data-ttu-id="c93b2-329">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-329">Default Value</span></span> | <span data-ttu-id="c93b2-330">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c93b2-331">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="c93b2-332">Diccionario de encabezados enviados con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="c93b2-333">El envío de encabezados en el explorador no funciona con WebSockets ni con el <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flujo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c93b2-334">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c93b2-335">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-336">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-337">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="c93b2-338">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="c93b2-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="c93b2-339">Azure App Service usa cookie para las sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="c93b2-340">Para obtener más información sobre CORS con SignalR , vea <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="c93b2-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-341">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-341">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-342">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-342">Java Option</span></span> | <span data-ttu-id="c93b2-343">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-343">Default Value</span></span> | <span data-ttu-id="c93b2-344">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c93b2-345">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c93b2-346">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-347">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-348">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c93b2-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c93b2-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c93b2-350">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-350">Empty</span></span> | <span data-ttu-id="c93b2-351">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c93b2-352">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c93b2-353">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c93b2-354">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c93b2-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c93b2-355">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c93b2-356">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-357">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c93b2-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c93b2-358">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="c93b2-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c93b2-359">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c93b2-360">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c93b2-361">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="c93b2-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c93b2-362">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="c93b2-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c93b2-363">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c93b2-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c93b2-364">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c93b2-365">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c93b2-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c93b2-366">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c93b2-367">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c93b2-368">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="c93b2-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c93b2-369">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c93b2-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c93b2-370">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-370">MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-371">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c93b2-372">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-373">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c93b2-374">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="c93b2-374">Configure server options</span></span>

<span data-ttu-id="c93b2-375">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="c93b2-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c93b2-376">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-376">Option</span></span> | <span data-ttu-id="c93b2-377">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-377">Default Value</span></span> | <span data-ttu-id="c93b2-378">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c93b2-379">30 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-379">30 seconds</span></span> | <span data-ttu-id="c93b2-380">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c93b2-381">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c93b2-382">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c93b2-383">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-383">15 seconds</span></span> | <span data-ttu-id="c93b2-384">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="c93b2-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c93b2-385">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-386">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-387">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-387">15 seconds</span></span> | <span data-ttu-id="c93b2-388">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c93b2-389">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c93b2-390">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c93b2-391">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="c93b2-391">All installed protocols</span></span> | <span data-ttu-id="c93b2-392">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-392">Protocols supported by this hub.</span></span> <span data-ttu-id="c93b2-393">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="c93b2-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c93b2-394">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c93b2-395">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="c93b2-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c93b2-396">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c93b2-397">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c93b2-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-398">32 KB</span></span> | <span data-ttu-id="c93b2-399">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="c93b2-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c93b2-400">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c93b2-401">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c93b2-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c93b2-402">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="c93b2-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c93b2-403">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c93b2-404">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c93b2-405">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="c93b2-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c93b2-406">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-406">Option</span></span> | <span data-ttu-id="c93b2-407">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-407">Default Value</span></span> | <span data-ttu-id="c93b2-408">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c93b2-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-409">32 KB</span></span> | <span data-ttu-id="c93b2-410">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="c93b2-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c93b2-411">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c93b2-412">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="c93b2-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c93b2-413">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c93b2-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-414">32 KB</span></span> | <span data-ttu-id="c93b2-415">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c93b2-416">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c93b2-417">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-417">All Transports are enabled.</span></span> | <span data-ttu-id="c93b2-418">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="c93b2-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c93b2-419">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-419">See below.</span></span> | <span data-ttu-id="c93b2-420">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c93b2-421">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-421">See below.</span></span> | <span data-ttu-id="c93b2-422">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c93b2-423">0</span><span class="sxs-lookup"><span data-stu-id="c93b2-423">0</span></span> | <span data-ttu-id="c93b2-424">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="c93b2-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c93b2-425">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="c93b2-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c93b2-426">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c93b2-427">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-427">Option</span></span> | <span data-ttu-id="c93b2-428">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-428">Default Value</span></span> | <span data-ttu-id="c93b2-429">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c93b2-430">90 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-430">90 seconds</span></span> | <span data-ttu-id="c93b2-431">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c93b2-432">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c93b2-433">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c93b2-434">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-434">Option</span></span> | <span data-ttu-id="c93b2-435">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-435">Default Value</span></span> | <span data-ttu-id="c93b2-436">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c93b2-437">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-437">5 seconds</span></span> | <span data-ttu-id="c93b2-438">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c93b2-439">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c93b2-440">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c93b2-441">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="c93b2-441">Configure client options</span></span>

<span data-ttu-id="c93b2-442">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c93b2-443">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="c93b2-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c93b2-444">registro</span><span class="sxs-lookup"><span data-stu-id="c93b2-444">Configure logging</span></span>

<span data-ttu-id="c93b2-445">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c93b2-446">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c93b2-447">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c93b2-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-448">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="c93b2-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c93b2-449">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c93b2-450">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="c93b2-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c93b2-451">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c93b2-452">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="c93b2-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c93b2-453">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="c93b2-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c93b2-454">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c93b2-455">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c93b2-456">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="c93b2-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c93b2-457">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-457">The following table lists the available log levels.</span></span> <span data-ttu-id="c93b2-458">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="c93b2-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c93b2-459">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c93b2-460">String</span><span class="sxs-lookup"><span data-stu-id="c93b2-460">String</span></span>                      | <span data-ttu-id="c93b2-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c93b2-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c93b2-462">`info`**o bien**`information`</span><span class="sxs-lookup"><span data-stu-id="c93b2-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c93b2-463">`warn`**o bien**`warning`</span><span class="sxs-lookup"><span data-stu-id="c93b2-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c93b2-464">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c93b2-465">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c93b2-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c93b2-466">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c93b2-467">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="c93b2-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c93b2-468">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="c93b2-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c93b2-469">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="c93b2-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c93b2-470">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c93b2-471">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="c93b2-471">Configure allowed transports</span></span>

<span data-ttu-id="c93b2-472">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c93b2-473">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c93b2-474">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c93b2-474">All transports are enabled by default.</span></span>

<span data-ttu-id="c93b2-475">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="c93b2-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c93b2-476">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c93b2-477">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="c93b2-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c93b2-478">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c93b2-479">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c93b2-480">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="c93b2-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c93b2-481">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="c93b2-481">Configure bearer authentication</span></span>

<span data-ttu-id="c93b2-482">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c93b2-483">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c93b2-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c93b2-484">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c93b2-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c93b2-485">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c93b2-486">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c93b2-487">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c93b2-488">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c93b2-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c93b2-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="c93b2-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c93b2-490">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c93b2-491">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="c93b2-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c93b2-492">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-493">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-494">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-494">Option</span></span> | <span data-ttu-id="c93b2-495">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-495">Default value</span></span> | <span data-ttu-id="c93b2-496">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c93b2-497">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-498">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-498">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-499">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-500">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-501">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-502">15 seconds</span></span> | <span data-ttu-id="c93b2-503">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-504">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-505">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-506">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-507">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-507">15 seconds</span></span> | <span data-ttu-id="c93b2-508">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-509">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-510">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c93b2-511">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="c93b2-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c93b2-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-513">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-513">Option</span></span> | <span data-ttu-id="c93b2-514">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-514">Default value</span></span> | <span data-ttu-id="c93b2-515">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c93b2-516">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-517">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-517">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-518">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c93b2-519">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-520">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c93b2-521">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-522">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-523">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-524">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-525">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-525">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-526">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-526">Option</span></span> | <span data-ttu-id="c93b2-527">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-527">Default value</span></span> | <span data-ttu-id="c93b2-528">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c93b2-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c93b2-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c93b2-530">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-531">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-531">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-532">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-533">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-534">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c93b2-535">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-535">15 seconds</span></span> | <span data-ttu-id="c93b2-536">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-537">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-538">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-539">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c93b2-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c93b2-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c93b2-541">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-542">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-543">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-544">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c93b2-545">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-545">Configure additional options</span></span>

<span data-ttu-id="c93b2-546">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="c93b2-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-547">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-548">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-548">.NET Option</span></span> |  <span data-ttu-id="c93b2-549">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-549">Default value</span></span> | <span data-ttu-id="c93b2-550">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c93b2-551">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c93b2-552">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-553">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-554">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c93b2-555">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-555">Empty</span></span> | <span data-ttu-id="c93b2-556">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c93b2-557">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-557">Empty</span></span> | <span data-ttu-id="c93b2-558">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c93b2-559">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-559">Empty</span></span> | <span data-ttu-id="c93b2-560">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c93b2-561">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-561">5 seconds</span></span> | <span data-ttu-id="c93b2-562">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-562">WebSockets only.</span></span> <span data-ttu-id="c93b2-563">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="c93b2-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c93b2-564">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c93b2-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c93b2-565">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-565">Empty</span></span> | <span data-ttu-id="c93b2-566">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c93b2-567">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c93b2-568">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="c93b2-569">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c93b2-570">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c93b2-571">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="c93b2-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c93b2-572">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c93b2-573">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c93b2-574">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c93b2-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c93b2-575">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c93b2-576">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="c93b2-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c93b2-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-578">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-578">JavaScript Option</span></span> | <span data-ttu-id="c93b2-579">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-579">Default Value</span></span> | <span data-ttu-id="c93b2-580">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c93b2-581">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c93b2-582">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c93b2-583">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-584">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-585">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-586">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-586">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-587">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-587">Java Option</span></span> | <span data-ttu-id="c93b2-588">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-588">Default Value</span></span> | <span data-ttu-id="c93b2-589">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c93b2-590">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c93b2-591">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-592">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-593">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c93b2-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c93b2-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c93b2-595">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-595">Empty</span></span> | <span data-ttu-id="c93b2-596">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c93b2-597">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c93b2-598">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c93b2-599">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c93b2-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c93b2-600">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c93b2-601">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-602">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c93b2-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c93b2-603">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="c93b2-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c93b2-604">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c93b2-605">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c93b2-606">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="c93b2-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c93b2-607">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="c93b2-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c93b2-608">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c93b2-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c93b2-609">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="c93b2-610">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c93b2-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c93b2-611">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c93b2-612">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c93b2-613">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="c93b2-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c93b2-614">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c93b2-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c93b2-615">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-615">MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-616">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c93b2-617">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-618">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c93b2-619">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="c93b2-619">Configure server options</span></span>

<span data-ttu-id="c93b2-620">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="c93b2-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c93b2-621">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-621">Option</span></span> | <span data-ttu-id="c93b2-622">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-622">Default Value</span></span> | <span data-ttu-id="c93b2-623">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c93b2-624">30 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-624">30 seconds</span></span> | <span data-ttu-id="c93b2-625">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c93b2-626">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c93b2-627">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c93b2-628">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-628">15 seconds</span></span> | <span data-ttu-id="c93b2-629">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="c93b2-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c93b2-630">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-631">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-632">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-632">15 seconds</span></span> | <span data-ttu-id="c93b2-633">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c93b2-634">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c93b2-635">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c93b2-636">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="c93b2-636">All installed protocols</span></span> | <span data-ttu-id="c93b2-637">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-637">Protocols supported by this hub.</span></span> <span data-ttu-id="c93b2-638">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="c93b2-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c93b2-639">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c93b2-640">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="c93b2-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c93b2-641">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c93b2-642">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c93b2-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-643">32 KB</span></span> | <span data-ttu-id="c93b2-644">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="c93b2-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c93b2-645">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c93b2-646">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c93b2-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c93b2-647">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="c93b2-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c93b2-648">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c93b2-649">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c93b2-650">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="c93b2-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c93b2-651">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-651">Option</span></span> | <span data-ttu-id="c93b2-652">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-652">Default Value</span></span> | <span data-ttu-id="c93b2-653">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c93b2-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-654">32 KB</span></span> | <span data-ttu-id="c93b2-655">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="c93b2-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c93b2-656">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c93b2-657">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="c93b2-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c93b2-658">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c93b2-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-659">32 KB</span></span> | <span data-ttu-id="c93b2-660">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c93b2-661">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c93b2-662">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-662">All Transports are enabled.</span></span> | <span data-ttu-id="c93b2-663">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="c93b2-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c93b2-664">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-664">See below.</span></span> | <span data-ttu-id="c93b2-665">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c93b2-666">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-666">See below.</span></span> | <span data-ttu-id="c93b2-667">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c93b2-668">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c93b2-669">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-669">Option</span></span> | <span data-ttu-id="c93b2-670">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-670">Default Value</span></span> | <span data-ttu-id="c93b2-671">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c93b2-672">90 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-672">90 seconds</span></span> | <span data-ttu-id="c93b2-673">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c93b2-674">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c93b2-675">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c93b2-676">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-676">Option</span></span> | <span data-ttu-id="c93b2-677">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-677">Default Value</span></span> | <span data-ttu-id="c93b2-678">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c93b2-679">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-679">5 seconds</span></span> | <span data-ttu-id="c93b2-680">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c93b2-681">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c93b2-682">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c93b2-683">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="c93b2-683">Configure client options</span></span>

<span data-ttu-id="c93b2-684">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c93b2-685">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="c93b2-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c93b2-686">registro</span><span class="sxs-lookup"><span data-stu-id="c93b2-686">Configure logging</span></span>

<span data-ttu-id="c93b2-687">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c93b2-688">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c93b2-689">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c93b2-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-690">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="c93b2-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c93b2-691">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c93b2-692">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="c93b2-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c93b2-693">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c93b2-694">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="c93b2-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c93b2-695">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="c93b2-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c93b2-696">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c93b2-697">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c93b2-698">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="c93b2-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c93b2-699">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-699">The following table lists the available log levels.</span></span> <span data-ttu-id="c93b2-700">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="c93b2-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c93b2-701">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c93b2-702">String</span><span class="sxs-lookup"><span data-stu-id="c93b2-702">String</span></span>                      | <span data-ttu-id="c93b2-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c93b2-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c93b2-704">`info`**o bien**`information`</span><span class="sxs-lookup"><span data-stu-id="c93b2-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c93b2-705">`warn`**o bien**`warning`</span><span class="sxs-lookup"><span data-stu-id="c93b2-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c93b2-706">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c93b2-707">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c93b2-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c93b2-708">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c93b2-709">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="c93b2-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c93b2-710">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="c93b2-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c93b2-711">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="c93b2-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c93b2-712">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c93b2-713">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="c93b2-713">Configure allowed transports</span></span>

<span data-ttu-id="c93b2-714">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c93b2-715">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c93b2-716">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c93b2-716">All transports are enabled by default.</span></span>

<span data-ttu-id="c93b2-717">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="c93b2-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c93b2-718">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c93b2-719">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="c93b2-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c93b2-720">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c93b2-721">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c93b2-722">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="c93b2-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c93b2-723">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="c93b2-723">Configure bearer authentication</span></span>

<span data-ttu-id="c93b2-724">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c93b2-725">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c93b2-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c93b2-726">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c93b2-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c93b2-727">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c93b2-728">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c93b2-729">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c93b2-730">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c93b2-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c93b2-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="c93b2-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c93b2-732">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c93b2-733">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="c93b2-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c93b2-734">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-735">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-736">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-736">Option</span></span> | <span data-ttu-id="c93b2-737">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-737">Default value</span></span> | <span data-ttu-id="c93b2-738">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c93b2-739">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-740">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-740">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-741">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-742">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-743">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-744">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-744">15 seconds</span></span> | <span data-ttu-id="c93b2-745">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-746">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-747">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-748">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-749">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-749">15 seconds</span></span> | <span data-ttu-id="c93b2-750">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-751">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-752">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c93b2-753">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="c93b2-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c93b2-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-755">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-755">Option</span></span> | <span data-ttu-id="c93b2-756">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-756">Default value</span></span> | <span data-ttu-id="c93b2-757">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c93b2-758">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-759">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-759">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-760">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c93b2-761">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-762">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c93b2-763">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-764">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-765">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-766">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-767">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-767">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-768">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-768">Option</span></span> | <span data-ttu-id="c93b2-769">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-769">Default value</span></span> | <span data-ttu-id="c93b2-770">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c93b2-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c93b2-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c93b2-772">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-773">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-773">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-774">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-775">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-776">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c93b2-777">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-777">15 seconds</span></span> | <span data-ttu-id="c93b2-778">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-779">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-780">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-781">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c93b2-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c93b2-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c93b2-783">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-784">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-785">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-786">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c93b2-787">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-787">Configure additional options</span></span>

<span data-ttu-id="c93b2-788">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="c93b2-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-789">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-790">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-790">.NET Option</span></span> |  <span data-ttu-id="c93b2-791">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-791">Default value</span></span> | <span data-ttu-id="c93b2-792">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c93b2-793">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c93b2-794">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-795">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-796">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c93b2-797">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-797">Empty</span></span> | <span data-ttu-id="c93b2-798">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c93b2-799">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-799">Empty</span></span> | <span data-ttu-id="c93b2-800">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c93b2-801">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-801">Empty</span></span> | <span data-ttu-id="c93b2-802">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c93b2-803">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-803">5 seconds</span></span> | <span data-ttu-id="c93b2-804">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-804">WebSockets only.</span></span> <span data-ttu-id="c93b2-805">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="c93b2-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c93b2-806">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c93b2-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c93b2-807">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-807">Empty</span></span> | <span data-ttu-id="c93b2-808">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c93b2-809">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c93b2-810">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="c93b2-811">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c93b2-812">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c93b2-813">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="c93b2-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c93b2-814">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c93b2-815">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c93b2-816">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c93b2-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c93b2-817">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c93b2-818">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="c93b2-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c93b2-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-820">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-820">JavaScript Option</span></span> | <span data-ttu-id="c93b2-821">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-821">Default Value</span></span> | <span data-ttu-id="c93b2-822">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c93b2-823">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c93b2-824">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c93b2-825">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-826">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-827">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-828">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-828">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-829">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-829">Java Option</span></span> | <span data-ttu-id="c93b2-830">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-830">Default Value</span></span> | <span data-ttu-id="c93b2-831">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c93b2-832">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c93b2-833">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-834">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-835">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c93b2-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c93b2-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c93b2-837">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-837">Empty</span></span> | <span data-ttu-id="c93b2-838">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c93b2-839">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c93b2-840">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c93b2-841">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c93b2-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c93b2-842">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c93b2-843">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-844">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c93b2-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c93b2-845">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="c93b2-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c93b2-846">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c93b2-847">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="c93b2-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c93b2-848">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="c93b2-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c93b2-849">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c93b2-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c93b2-850">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c93b2-851">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c93b2-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c93b2-852">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c93b2-853">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c93b2-854">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-854">MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-855">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c93b2-856">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-857">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c93b2-858">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="c93b2-858">Configure server options</span></span>

<span data-ttu-id="c93b2-859">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="c93b2-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c93b2-860">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-860">Option</span></span> | <span data-ttu-id="c93b2-861">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-861">Default Value</span></span> | <span data-ttu-id="c93b2-862">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c93b2-863">30 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-863">30 seconds</span></span> | <span data-ttu-id="c93b2-864">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c93b2-865">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c93b2-866">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c93b2-867">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-867">15 seconds</span></span> | <span data-ttu-id="c93b2-868">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="c93b2-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c93b2-869">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-870">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-871">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-871">15 seconds</span></span> | <span data-ttu-id="c93b2-872">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c93b2-873">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c93b2-874">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c93b2-875">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="c93b2-875">All installed protocols</span></span> | <span data-ttu-id="c93b2-876">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-876">Protocols supported by this hub.</span></span> <span data-ttu-id="c93b2-877">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="c93b2-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c93b2-878">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c93b2-879">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="c93b2-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c93b2-880">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c93b2-881">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c93b2-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c93b2-882">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="c93b2-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c93b2-883">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c93b2-884">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c93b2-885">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="c93b2-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c93b2-886">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-886">Option</span></span> | <span data-ttu-id="c93b2-887">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-887">Default Value</span></span> | <span data-ttu-id="c93b2-888">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c93b2-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-889">32 KB</span></span> | <span data-ttu-id="c93b2-890">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="c93b2-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c93b2-891">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c93b2-892">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="c93b2-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c93b2-893">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c93b2-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-894">32 KB</span></span> | <span data-ttu-id="c93b2-895">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="c93b2-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c93b2-896">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c93b2-897">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-897">All Transports are enabled.</span></span> | <span data-ttu-id="c93b2-898">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="c93b2-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c93b2-899">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-899">See below.</span></span> | <span data-ttu-id="c93b2-900">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c93b2-901">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-901">See below.</span></span> | <span data-ttu-id="c93b2-902">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c93b2-903">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c93b2-904">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-904">Option</span></span> | <span data-ttu-id="c93b2-905">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-905">Default Value</span></span> | <span data-ttu-id="c93b2-906">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c93b2-907">90 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-907">90 seconds</span></span> | <span data-ttu-id="c93b2-908">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c93b2-909">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c93b2-910">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c93b2-911">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-911">Option</span></span> | <span data-ttu-id="c93b2-912">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-912">Default Value</span></span> | <span data-ttu-id="c93b2-913">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c93b2-914">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-914">5 seconds</span></span> | <span data-ttu-id="c93b2-915">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c93b2-916">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c93b2-917">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c93b2-918">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="c93b2-918">Configure client options</span></span>

<span data-ttu-id="c93b2-919">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c93b2-920">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="c93b2-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c93b2-921">registro</span><span class="sxs-lookup"><span data-stu-id="c93b2-921">Configure logging</span></span>

<span data-ttu-id="c93b2-922">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c93b2-923">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c93b2-924">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c93b2-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-925">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="c93b2-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c93b2-926">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c93b2-927">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="c93b2-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c93b2-928">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c93b2-929">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="c93b2-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c93b2-930">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="c93b2-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c93b2-931">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c93b2-932">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c93b2-933">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c93b2-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c93b2-934">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c93b2-935">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="c93b2-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c93b2-936">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="c93b2-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c93b2-937">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="c93b2-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c93b2-938">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c93b2-939">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="c93b2-939">Configure allowed transports</span></span>

<span data-ttu-id="c93b2-940">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c93b2-941">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c93b2-942">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c93b2-942">All transports are enabled by default.</span></span>

<span data-ttu-id="c93b2-943">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="c93b2-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c93b2-944">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c93b2-945">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="c93b2-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c93b2-946">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="c93b2-946">Configure bearer authentication</span></span>

<span data-ttu-id="c93b2-947">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c93b2-948">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c93b2-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c93b2-949">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c93b2-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c93b2-950">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c93b2-951">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c93b2-952">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c93b2-953">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c93b2-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c93b2-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="c93b2-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c93b2-955">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c93b2-956">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="c93b2-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c93b2-957">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-958">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-959">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-959">Option</span></span> | <span data-ttu-id="c93b2-960">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-960">Default value</span></span> | <span data-ttu-id="c93b2-961">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c93b2-962">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-963">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-963">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-964">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-965">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-966">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-967">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-967">15 seconds</span></span> | <span data-ttu-id="c93b2-968">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-969">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-970">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-971">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-972">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-972">15 seconds</span></span> | <span data-ttu-id="c93b2-973">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-974">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-975">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c93b2-976">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="c93b2-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c93b2-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-978">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-978">Option</span></span> | <span data-ttu-id="c93b2-979">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-979">Default value</span></span> | <span data-ttu-id="c93b2-980">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c93b2-981">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-982">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-982">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-983">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c93b2-984">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-985">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c93b2-986">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-987">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-988">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-989">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-990">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-990">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-991">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-991">Option</span></span> | <span data-ttu-id="c93b2-992">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-992">Default value</span></span> | <span data-ttu-id="c93b2-993">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c93b2-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c93b2-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c93b2-995">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-996">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-996">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-997">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-998">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-999">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c93b2-1000">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1000">15 seconds</span></span> | <span data-ttu-id="c93b2-1001">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-1002">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-1003">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-1004">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c93b2-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c93b2-1006">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-1007">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c93b2-1008">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c93b2-1009">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c93b2-1010">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-1010">Configure additional options</span></span>

<span data-ttu-id="c93b2-1011">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-1013">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-1013">.NET Option</span></span> |  <span data-ttu-id="c93b2-1014">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1014">Default value</span></span> | <span data-ttu-id="c93b2-1015">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c93b2-1016">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c93b2-1017">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1018">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1019">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c93b2-1020">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1020">Empty</span></span> | <span data-ttu-id="c93b2-1021">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c93b2-1022">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1022">Empty</span></span> | <span data-ttu-id="c93b2-1023">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c93b2-1024">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1024">Empty</span></span> | <span data-ttu-id="c93b2-1025">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c93b2-1026">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1026">5 seconds</span></span> | <span data-ttu-id="c93b2-1027">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1027">WebSockets only.</span></span> <span data-ttu-id="c93b2-1028">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c93b2-1029">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c93b2-1030">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1030">Empty</span></span> | <span data-ttu-id="c93b2-1031">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c93b2-1032">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c93b2-1033">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="c93b2-1034">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c93b2-1035">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c93b2-1036">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="c93b2-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c93b2-1037">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c93b2-1038">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c93b2-1039">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c93b2-1040">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c93b2-1041">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c93b2-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-1043">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-1043">JavaScript Option</span></span> | <span data-ttu-id="c93b2-1044">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1044">Default Value</span></span> | <span data-ttu-id="c93b2-1045">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c93b2-1046">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c93b2-1047">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c93b2-1048">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1049">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1050">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-1051">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-1052">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-1052">Java Option</span></span> | <span data-ttu-id="c93b2-1053">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1053">Default Value</span></span> | <span data-ttu-id="c93b2-1054">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c93b2-1055">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c93b2-1056">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1057">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1058">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c93b2-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c93b2-1060">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1060">Empty</span></span> | <span data-ttu-id="c93b2-1061">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c93b2-1062">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c93b2-1063">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c93b2-1064">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c93b2-1065">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c93b2-1066">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-1067">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c93b2-1068">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c93b2-1069">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c93b2-1070">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c93b2-1071">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c93b2-1072">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c93b2-1073">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c93b2-1074">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c93b2-1075">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c93b2-1076">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c93b2-1077">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="c93b2-1077">MessagePack serialization options</span></span>

<span data-ttu-id="c93b2-1078">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c93b2-1079">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-1080">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c93b2-1081">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="c93b2-1081">Configure server options</span></span>

<span data-ttu-id="c93b2-1082">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c93b2-1083">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1083">Option</span></span> | <span data-ttu-id="c93b2-1084">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1084">Default Value</span></span> | <span data-ttu-id="c93b2-1085">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-1086">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1086">15 seconds</span></span> | <span data-ttu-id="c93b2-1087">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c93b2-1088">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-1089">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c93b2-1090">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1090">15 seconds</span></span> | <span data-ttu-id="c93b2-1091">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c93b2-1092">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c93b2-1093">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c93b2-1094">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="c93b2-1094">All installed protocols</span></span> | <span data-ttu-id="c93b2-1095">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="c93b2-1096">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c93b2-1097">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c93b2-1098">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c93b2-1099">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c93b2-1100">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c93b2-1101">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="c93b2-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c93b2-1102">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c93b2-1103">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c93b2-1104">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c93b2-1105">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1105">Option</span></span> | <span data-ttu-id="c93b2-1106">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1106">Default Value</span></span> | <span data-ttu-id="c93b2-1107">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c93b2-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-1108">32 KB</span></span> | <span data-ttu-id="c93b2-1109">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c93b2-1110">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c93b2-1111">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c93b2-1112">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c93b2-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="c93b2-1113">32 KB</span></span> | <span data-ttu-id="c93b2-1114">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c93b2-1115">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c93b2-1116">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1116">All Transports are enabled.</span></span> | <span data-ttu-id="c93b2-1117">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c93b2-1118">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1118">See below.</span></span> | <span data-ttu-id="c93b2-1119">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c93b2-1120">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1120">See below.</span></span> | <span data-ttu-id="c93b2-1121">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c93b2-1122">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c93b2-1123">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1123">Option</span></span> | <span data-ttu-id="c93b2-1124">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1124">Default Value</span></span> | <span data-ttu-id="c93b2-1125">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c93b2-1126">90 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1126">90 seconds</span></span> | <span data-ttu-id="c93b2-1127">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c93b2-1128">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c93b2-1129">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c93b2-1130">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1130">Option</span></span> | <span data-ttu-id="c93b2-1131">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1131">Default Value</span></span> | <span data-ttu-id="c93b2-1132">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c93b2-1133">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1133">5 seconds</span></span> | <span data-ttu-id="c93b2-1134">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c93b2-1135">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c93b2-1136">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c93b2-1137">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="c93b2-1137">Configure client options</span></span>

<span data-ttu-id="c93b2-1138">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c93b2-1139">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c93b2-1140">registro</span><span class="sxs-lookup"><span data-stu-id="c93b2-1140">Configure logging</span></span>

<span data-ttu-id="c93b2-1141">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c93b2-1142">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c93b2-1143">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c93b2-1144">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c93b2-1145">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c93b2-1146">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c93b2-1147">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c93b2-1148">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c93b2-1149">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c93b2-1150">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c93b2-1151">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c93b2-1152">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c93b2-1153">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c93b2-1154">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c93b2-1155">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c93b2-1156">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c93b2-1157">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c93b2-1158">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1158">Configure allowed transports</span></span>

<span data-ttu-id="c93b2-1159">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c93b2-1160">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c93b2-1161">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="c93b2-1162">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c93b2-1163">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c93b2-1164">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="c93b2-1164">Configure bearer authentication</span></span>

<span data-ttu-id="c93b2-1165">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c93b2-1166">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c93b2-1167">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c93b2-1168">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c93b2-1169">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c93b2-1170">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c93b2-1171">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c93b2-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c93b2-1173">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c93b2-1174">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="c93b2-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c93b2-1175">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-1177">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1177">Option</span></span> | <span data-ttu-id="c93b2-1178">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1178">Default value</span></span> | <span data-ttu-id="c93b2-1179">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c93b2-1180">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-1181">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1181">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-1182">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-1183">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-1184">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c93b2-1185">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1185">15 seconds</span></span> | <span data-ttu-id="c93b2-1186">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-1187">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c93b2-1188">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-1189">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c93b2-1190">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c93b2-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-1192">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1192">Option</span></span> | <span data-ttu-id="c93b2-1193">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1193">Default value</span></span> | <span data-ttu-id="c93b2-1194">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c93b2-1195">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-1196">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1196">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-1197">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c93b2-1198">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-1199">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-1200">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-1201">Opción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1201">Option</span></span> | <span data-ttu-id="c93b2-1202">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1202">Default value</span></span> | <span data-ttu-id="c93b2-1203">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c93b2-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c93b2-1205">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="c93b2-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c93b2-1206">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1206">Timeout for server activity.</span></span> <span data-ttu-id="c93b2-1207">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-1208">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c93b2-1209">El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c93b2-1210">15 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1210">15 seconds</span></span> | <span data-ttu-id="c93b2-1211">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="c93b2-1212">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c93b2-1213">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c93b2-1214">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c93b2-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c93b2-1215">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-1215">Configure additional options</span></span>

<span data-ttu-id="c93b2-1216">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="c93b2-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c93b2-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c93b2-1218">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="c93b2-1218">.NET Option</span></span> |  <span data-ttu-id="c93b2-1219">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1219">Default value</span></span> | <span data-ttu-id="c93b2-1220">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c93b2-1221">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c93b2-1222">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1223">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1224">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c93b2-1225">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1225">Empty</span></span> | <span data-ttu-id="c93b2-1226">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c93b2-1227">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1227">Empty</span></span> | <span data-ttu-id="c93b2-1228">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c93b2-1229">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1229">Empty</span></span> | <span data-ttu-id="c93b2-1230">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c93b2-1231">5 segundos</span><span class="sxs-lookup"><span data-stu-id="c93b2-1231">5 seconds</span></span> | <span data-ttu-id="c93b2-1232">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1232">WebSockets only.</span></span> <span data-ttu-id="c93b2-1233">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c93b2-1234">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c93b2-1235">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1235">Empty</span></span> | <span data-ttu-id="c93b2-1236">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c93b2-1237">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c93b2-1238">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="c93b2-1239">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c93b2-1240">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c93b2-1241">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="c93b2-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c93b2-1242">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c93b2-1243">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c93b2-1244">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c93b2-1245">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c93b2-1246">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c93b2-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c93b2-1248">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c93b2-1248">JavaScript Option</span></span> | <span data-ttu-id="c93b2-1249">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1249">Default Value</span></span> | <span data-ttu-id="c93b2-1250">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c93b2-1251">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c93b2-1252">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c93b2-1253">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1254">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1255">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c93b2-1256">Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="c93b2-1257">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="c93b2-1257">Java Option</span></span> | <span data-ttu-id="c93b2-1258">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="c93b2-1258">Default Value</span></span> | <span data-ttu-id="c93b2-1259">Descripción</span><span class="sxs-lookup"><span data-stu-id="c93b2-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c93b2-1260">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c93b2-1261">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c93b2-1262">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c93b2-1263">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="c93b2-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c93b2-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c93b2-1265">Vacío</span><span class="sxs-lookup"><span data-stu-id="c93b2-1265">Empty</span></span> | <span data-ttu-id="c93b2-1266">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="c93b2-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c93b2-1267">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c93b2-1268">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="c93b2-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c93b2-1269">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c93b2-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c93b2-1270">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c93b2-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
