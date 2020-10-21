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
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326590"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="b14e0-103">Configuración de SignalR en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b14e0-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b14e0-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-105">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b14e0-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b14e0-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="b14e0-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b14e0-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b14e0-108">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b14e0-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b14e0-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b14e0-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="b14e0-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b14e0-111">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b14e0-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b14e0-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b14e0-113">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b14e0-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b14e0-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b14e0-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b14e0-116">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="b14e0-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b14e0-117">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b14e0-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b14e0-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-118">MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b14e0-120">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b14e0-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="b14e0-122">Configure server options</span></span>

<span data-ttu-id="b14e0-123">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="b14e0-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b14e0-124">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-124">Option</span></span> | <span data-ttu-id="b14e0-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-125">Default Value</span></span> | <span data-ttu-id="b14e0-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b14e0-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-127">30 seconds</span></span> | <span data-ttu-id="b14e0-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b14e0-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b14e0-130">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b14e0-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-131">15 seconds</span></span> | <span data-ttu-id="b14e0-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="b14e0-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b14e0-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-134">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-135">15 seconds</span></span> | <span data-ttu-id="b14e0-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b14e0-137">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b14e0-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b14e0-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b14e0-139">All installed protocols</span></span> | <span data-ttu-id="b14e0-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-140">Protocols supported by this hub.</span></span> <span data-ttu-id="b14e0-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="b14e0-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b14e0-142">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b14e0-143">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="b14e0-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b14e0-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b14e0-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b14e0-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-146">32 KB</span></span> | <span data-ttu-id="b14e0-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="b14e0-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="b14e0-148">1</span><span class="sxs-lookup"><span data-stu-id="b14e0-148">1</span></span> | <span data-ttu-id="b14e0-149">Número máximo de métodos de concentrador a los que cada cliente puede llamar en paralelo antes de poner en cola.</span><span class="sxs-lookup"><span data-stu-id="b14e0-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="b14e0-150">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b14e0-151">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b14e0-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b14e0-152">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="b14e0-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b14e0-153">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b14e0-154">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b14e0-155">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b14e0-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b14e0-156">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-156">Option</span></span> | <span data-ttu-id="b14e0-157">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-157">Default Value</span></span> | <span data-ttu-id="b14e0-158">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b14e0-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-159">32 KB</span></span> | <span data-ttu-id="b14e0-160">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="b14e0-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b14e0-161">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b14e0-162">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="b14e0-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b14e0-163">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b14e0-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-164">32 KB</span></span> | <span data-ttu-id="b14e0-165">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b14e0-166">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b14e0-167">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-167">All Transports are enabled.</span></span> | <span data-ttu-id="b14e0-168">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="b14e0-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b14e0-169">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-169">See below.</span></span> | <span data-ttu-id="b14e0-170">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b14e0-171">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-171">See below.</span></span> | <span data-ttu-id="b14e0-172">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b14e0-173">0</span><span class="sxs-lookup"><span data-stu-id="b14e0-173">0</span></span> | <span data-ttu-id="b14e0-174">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b14e0-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b14e0-175">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="b14e0-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b14e0-176">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b14e0-177">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-177">Option</span></span> | <span data-ttu-id="b14e0-178">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-178">Default Value</span></span> | <span data-ttu-id="b14e0-179">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b14e0-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-180">90 seconds</span></span> | <span data-ttu-id="b14e0-181">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b14e0-182">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b14e0-183">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b14e0-184">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-184">Option</span></span> | <span data-ttu-id="b14e0-185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-185">Default Value</span></span> | <span data-ttu-id="b14e0-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b14e0-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-187">5 seconds</span></span> | <span data-ttu-id="b14e0-188">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b14e0-189">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b14e0-190">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b14e0-191">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="b14e0-191">Configure client options</span></span>

<span data-ttu-id="b14e0-192">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b14e0-193">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="b14e0-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b14e0-194">registro</span><span class="sxs-lookup"><span data-stu-id="b14e0-194">Configure logging</span></span>

<span data-ttu-id="b14e0-195">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b14e0-196">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b14e0-197">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b14e0-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-198">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="b14e0-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b14e0-199">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b14e0-200">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b14e0-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b14e0-201">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b14e0-202">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="b14e0-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b14e0-203">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="b14e0-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b14e0-204">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b14e0-205">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b14e0-206">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="b14e0-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b14e0-207">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-207">The following table lists the available log levels.</span></span> <span data-ttu-id="b14e0-208">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="b14e0-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b14e0-209">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b14e0-210">String</span><span class="sxs-lookup"><span data-stu-id="b14e0-210">String</span></span>                      | <span data-ttu-id="b14e0-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b14e0-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b14e0-212">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="b14e0-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b14e0-213">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="b14e0-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b14e0-214">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b14e0-215">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b14e0-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b14e0-216">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b14e0-217">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="b14e0-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b14e0-218">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="b14e0-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b14e0-219">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="b14e0-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b14e0-220">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b14e0-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b14e0-221">Configure allowed transports</span></span>

<span data-ttu-id="b14e0-222">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b14e0-223">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b14e0-224">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b14e0-224">All transports are enabled by default.</span></span>

<span data-ttu-id="b14e0-225">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="b14e0-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b14e0-226">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b14e0-227">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="b14e0-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b14e0-228">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b14e0-229">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b14e0-230">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="b14e0-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b14e0-231">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="b14e0-231">Configure bearer authentication</span></span>

<span data-ttu-id="b14e0-232">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b14e0-233">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b14e0-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b14e0-234">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b14e0-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b14e0-235">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b14e0-236">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b14e0-237">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b14e0-238">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b14e0-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b14e0-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b14e0-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b14e0-240">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b14e0-241">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="b14e0-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b14e0-242">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-243">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-244">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-244">Option</span></span> | <span data-ttu-id="b14e0-245">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-245">Default value</span></span> | <span data-ttu-id="b14e0-246">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b14e0-247">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-248">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-248">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-249">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-250">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-251">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-252">15 seconds</span></span> | <span data-ttu-id="b14e0-253">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-254">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-255">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-256">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-257">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-257">15 seconds</span></span> | <span data-ttu-id="b14e0-258">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-259">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-260">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b14e0-261">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="b14e0-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b14e0-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-263">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-263">Option</span></span> | <span data-ttu-id="b14e0-264">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-264">Default value</span></span> | <span data-ttu-id="b14e0-265">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b14e0-266">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-267">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-267">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-268">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b14e0-269">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-270">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b14e0-271">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-272">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-273">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-274">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-275">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-275">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-276">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-276">Option</span></span> | <span data-ttu-id="b14e0-277">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-277">Default value</span></span> | <span data-ttu-id="b14e0-278">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b14e0-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b14e0-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b14e0-280">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-281">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-281">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-282">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-283">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-284">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b14e0-285">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-285">15 seconds</span></span> | <span data-ttu-id="b14e0-286">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-287">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-288">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-289">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b14e0-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b14e0-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b14e0-291">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-292">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-293">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-294">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b14e0-295">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-295">Configure additional options</span></span>

<span data-ttu-id="b14e0-296">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="b14e0-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-297">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-298">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-298">.NET Option</span></span> |  <span data-ttu-id="b14e0-299">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-299">Default value</span></span> | <span data-ttu-id="b14e0-300">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b14e0-301">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b14e0-302">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-303">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-304">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b14e0-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-305">Empty</span></span> | <span data-ttu-id="b14e0-306">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b14e0-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-307">Empty</span></span> | <span data-ttu-id="b14e0-308">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b14e0-309">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-309">Empty</span></span> | <span data-ttu-id="b14e0-310">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b14e0-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-311">5 seconds</span></span> | <span data-ttu-id="b14e0-312">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-312">WebSockets only.</span></span> <span data-ttu-id="b14e0-313">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="b14e0-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b14e0-314">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b14e0-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b14e0-315">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-315">Empty</span></span> | <span data-ttu-id="b14e0-316">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b14e0-317">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b14e0-318">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="b14e0-319">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b14e0-320">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b14e0-321">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="b14e0-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b14e0-322">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b14e0-323">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b14e0-324">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="b14e0-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b14e0-325">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b14e0-326">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="b14e0-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b14e0-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-328">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-328">JavaScript Option</span></span> | <span data-ttu-id="b14e0-329">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-329">Default Value</span></span> | <span data-ttu-id="b14e0-330">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b14e0-331">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b14e0-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="b14e0-333">Diccionario de encabezados enviados con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="b14e0-334">El envío de encabezados en el explorador no funciona con WebSockets ni con el <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flujo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b14e0-335">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b14e0-336">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-337">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-338">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="b14e0-339">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="b14e0-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="b14e0-340">Azure App Service usa cookie para las sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="b14e0-341">Para obtener más información sobre CORS con SignalR , vea <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="b14e0-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-342">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-342">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-343">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-343">Java Option</span></span> | <span data-ttu-id="b14e0-344">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-344">Default Value</span></span> | <span data-ttu-id="b14e0-345">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b14e0-346">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b14e0-347">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-348">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-349">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b14e0-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b14e0-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b14e0-351">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-351">Empty</span></span> | <span data-ttu-id="b14e0-352">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b14e0-353">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

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

<span data-ttu-id="b14e0-354">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="b14e0-355">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b14e0-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b14e0-356">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b14e0-357">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-358">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b14e0-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b14e0-359">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="b14e0-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b14e0-360">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b14e0-361">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b14e0-362">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b14e0-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b14e0-363">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="b14e0-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b14e0-364">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b14e0-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b14e0-365">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b14e0-366">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b14e0-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b14e0-367">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b14e0-368">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b14e0-369">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="b14e0-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b14e0-370">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b14e0-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b14e0-371">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-371">MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-372">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b14e0-373">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-374">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b14e0-375">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="b14e0-375">Configure server options</span></span>

<span data-ttu-id="b14e0-376">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="b14e0-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b14e0-377">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-377">Option</span></span> | <span data-ttu-id="b14e0-378">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-378">Default Value</span></span> | <span data-ttu-id="b14e0-379">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b14e0-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-380">30 seconds</span></span> | <span data-ttu-id="b14e0-381">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b14e0-382">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b14e0-383">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b14e0-384">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-384">15 seconds</span></span> | <span data-ttu-id="b14e0-385">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="b14e0-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b14e0-386">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-387">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-388">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-388">15 seconds</span></span> | <span data-ttu-id="b14e0-389">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b14e0-390">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b14e0-391">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b14e0-392">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b14e0-392">All installed protocols</span></span> | <span data-ttu-id="b14e0-393">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-393">Protocols supported by this hub.</span></span> <span data-ttu-id="b14e0-394">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="b14e0-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b14e0-395">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b14e0-396">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="b14e0-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b14e0-397">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b14e0-398">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b14e0-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-399">32 KB</span></span> | <span data-ttu-id="b14e0-400">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="b14e0-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b14e0-401">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b14e0-402">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b14e0-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b14e0-403">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="b14e0-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b14e0-404">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b14e0-405">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b14e0-406">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b14e0-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b14e0-407">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-407">Option</span></span> | <span data-ttu-id="b14e0-408">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-408">Default Value</span></span> | <span data-ttu-id="b14e0-409">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b14e0-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-410">32 KB</span></span> | <span data-ttu-id="b14e0-411">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="b14e0-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b14e0-412">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b14e0-413">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="b14e0-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b14e0-414">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b14e0-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-415">32 KB</span></span> | <span data-ttu-id="b14e0-416">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b14e0-417">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b14e0-418">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-418">All Transports are enabled.</span></span> | <span data-ttu-id="b14e0-419">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="b14e0-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b14e0-420">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-420">See below.</span></span> | <span data-ttu-id="b14e0-421">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b14e0-422">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-422">See below.</span></span> | <span data-ttu-id="b14e0-423">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b14e0-424">0</span><span class="sxs-lookup"><span data-stu-id="b14e0-424">0</span></span> | <span data-ttu-id="b14e0-425">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b14e0-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b14e0-426">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="b14e0-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b14e0-427">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b14e0-428">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-428">Option</span></span> | <span data-ttu-id="b14e0-429">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-429">Default Value</span></span> | <span data-ttu-id="b14e0-430">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b14e0-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-431">90 seconds</span></span> | <span data-ttu-id="b14e0-432">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b14e0-433">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b14e0-434">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b14e0-435">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-435">Option</span></span> | <span data-ttu-id="b14e0-436">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-436">Default Value</span></span> | <span data-ttu-id="b14e0-437">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b14e0-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-438">5 seconds</span></span> | <span data-ttu-id="b14e0-439">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b14e0-440">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b14e0-441">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b14e0-442">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="b14e0-442">Configure client options</span></span>

<span data-ttu-id="b14e0-443">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b14e0-444">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="b14e0-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b14e0-445">registro</span><span class="sxs-lookup"><span data-stu-id="b14e0-445">Configure logging</span></span>

<span data-ttu-id="b14e0-446">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b14e0-447">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b14e0-448">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b14e0-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-449">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="b14e0-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b14e0-450">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b14e0-451">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b14e0-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b14e0-452">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b14e0-453">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="b14e0-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b14e0-454">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="b14e0-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b14e0-455">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b14e0-456">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b14e0-457">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="b14e0-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b14e0-458">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-458">The following table lists the available log levels.</span></span> <span data-ttu-id="b14e0-459">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="b14e0-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b14e0-460">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b14e0-461">String</span><span class="sxs-lookup"><span data-stu-id="b14e0-461">String</span></span>                      | <span data-ttu-id="b14e0-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b14e0-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b14e0-463">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="b14e0-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b14e0-464">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="b14e0-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b14e0-465">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b14e0-466">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b14e0-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b14e0-467">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b14e0-468">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="b14e0-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b14e0-469">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="b14e0-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b14e0-470">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="b14e0-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b14e0-471">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b14e0-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b14e0-472">Configure allowed transports</span></span>

<span data-ttu-id="b14e0-473">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b14e0-474">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b14e0-475">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b14e0-475">All transports are enabled by default.</span></span>

<span data-ttu-id="b14e0-476">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="b14e0-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b14e0-477">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b14e0-478">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="b14e0-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b14e0-479">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b14e0-480">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b14e0-481">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="b14e0-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b14e0-482">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="b14e0-482">Configure bearer authentication</span></span>

<span data-ttu-id="b14e0-483">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b14e0-484">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b14e0-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b14e0-485">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b14e0-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b14e0-486">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b14e0-487">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b14e0-488">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b14e0-489">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b14e0-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b14e0-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b14e0-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b14e0-491">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b14e0-492">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="b14e0-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b14e0-493">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-494">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-495">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-495">Option</span></span> | <span data-ttu-id="b14e0-496">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-496">Default value</span></span> | <span data-ttu-id="b14e0-497">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b14e0-498">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-499">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-499">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-500">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-501">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-502">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-503">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-503">15 seconds</span></span> | <span data-ttu-id="b14e0-504">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-505">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-506">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-507">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-508">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-508">15 seconds</span></span> | <span data-ttu-id="b14e0-509">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-510">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-511">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b14e0-512">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="b14e0-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b14e0-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-514">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-514">Option</span></span> | <span data-ttu-id="b14e0-515">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-515">Default value</span></span> | <span data-ttu-id="b14e0-516">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b14e0-517">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-518">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-518">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-519">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b14e0-520">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-521">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b14e0-522">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-523">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-524">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-525">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-526">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-526">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-527">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-527">Option</span></span> | <span data-ttu-id="b14e0-528">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-528">Default value</span></span> | <span data-ttu-id="b14e0-529">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b14e0-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b14e0-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b14e0-531">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-532">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-532">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-533">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-534">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-535">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b14e0-536">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-536">15 seconds</span></span> | <span data-ttu-id="b14e0-537">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-538">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-539">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-540">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b14e0-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b14e0-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b14e0-542">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-543">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-544">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-545">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b14e0-546">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-546">Configure additional options</span></span>

<span data-ttu-id="b14e0-547">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="b14e0-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-548">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-549">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-549">.NET Option</span></span> |  <span data-ttu-id="b14e0-550">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-550">Default value</span></span> | <span data-ttu-id="b14e0-551">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b14e0-552">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b14e0-553">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-554">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-555">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b14e0-556">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-556">Empty</span></span> | <span data-ttu-id="b14e0-557">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b14e0-558">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-558">Empty</span></span> | <span data-ttu-id="b14e0-559">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b14e0-560">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-560">Empty</span></span> | <span data-ttu-id="b14e0-561">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b14e0-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-562">5 seconds</span></span> | <span data-ttu-id="b14e0-563">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-563">WebSockets only.</span></span> <span data-ttu-id="b14e0-564">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="b14e0-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b14e0-565">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b14e0-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b14e0-566">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-566">Empty</span></span> | <span data-ttu-id="b14e0-567">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b14e0-568">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b14e0-569">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="b14e0-570">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b14e0-571">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b14e0-572">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="b14e0-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b14e0-573">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b14e0-574">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b14e0-575">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="b14e0-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b14e0-576">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b14e0-577">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="b14e0-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b14e0-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-579">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-579">JavaScript Option</span></span> | <span data-ttu-id="b14e0-580">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-580">Default Value</span></span> | <span data-ttu-id="b14e0-581">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b14e0-582">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b14e0-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b14e0-584">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b14e0-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-587">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-588">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-588">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-589">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-589">Java Option</span></span> | <span data-ttu-id="b14e0-590">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-590">Default Value</span></span> | <span data-ttu-id="b14e0-591">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b14e0-592">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b14e0-593">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-594">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-595">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b14e0-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b14e0-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b14e0-597">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-597">Empty</span></span> | <span data-ttu-id="b14e0-598">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b14e0-599">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b14e0-600">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b14e0-601">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b14e0-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b14e0-602">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b14e0-603">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-604">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b14e0-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b14e0-605">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="b14e0-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b14e0-606">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b14e0-607">`AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b14e0-608">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b14e0-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b14e0-609">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="b14e0-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b14e0-610">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b14e0-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b14e0-611">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="b14e0-612">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b14e0-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b14e0-613">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b14e0-614">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b14e0-615">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="b14e0-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b14e0-616">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b14e0-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b14e0-617">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-617">MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-618">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b14e0-619">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-620">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b14e0-621">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="b14e0-621">Configure server options</span></span>

<span data-ttu-id="b14e0-622">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="b14e0-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b14e0-623">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-623">Option</span></span> | <span data-ttu-id="b14e0-624">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-624">Default Value</span></span> | <span data-ttu-id="b14e0-625">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b14e0-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-626">30 seconds</span></span> | <span data-ttu-id="b14e0-627">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b14e0-628">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b14e0-629">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b14e0-630">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-630">15 seconds</span></span> | <span data-ttu-id="b14e0-631">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="b14e0-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b14e0-632">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-633">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-634">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-634">15 seconds</span></span> | <span data-ttu-id="b14e0-635">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b14e0-636">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b14e0-637">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b14e0-638">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b14e0-638">All installed protocols</span></span> | <span data-ttu-id="b14e0-639">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-639">Protocols supported by this hub.</span></span> <span data-ttu-id="b14e0-640">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="b14e0-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b14e0-641">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b14e0-642">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="b14e0-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b14e0-643">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b14e0-644">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b14e0-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-645">32 KB</span></span> | <span data-ttu-id="b14e0-646">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="b14e0-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b14e0-647">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b14e0-648">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b14e0-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b14e0-649">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="b14e0-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b14e0-650">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b14e0-651">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b14e0-652">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b14e0-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b14e0-653">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-653">Option</span></span> | <span data-ttu-id="b14e0-654">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-654">Default Value</span></span> | <span data-ttu-id="b14e0-655">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b14e0-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-656">32 KB</span></span> | <span data-ttu-id="b14e0-657">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="b14e0-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b14e0-658">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b14e0-659">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="b14e0-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b14e0-660">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b14e0-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-661">32 KB</span></span> | <span data-ttu-id="b14e0-662">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b14e0-663">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b14e0-664">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-664">All Transports are enabled.</span></span> | <span data-ttu-id="b14e0-665">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="b14e0-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b14e0-666">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-666">See below.</span></span> | <span data-ttu-id="b14e0-667">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b14e0-668">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-668">See below.</span></span> | <span data-ttu-id="b14e0-669">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b14e0-670">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b14e0-671">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-671">Option</span></span> | <span data-ttu-id="b14e0-672">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-672">Default Value</span></span> | <span data-ttu-id="b14e0-673">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b14e0-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-674">90 seconds</span></span> | <span data-ttu-id="b14e0-675">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b14e0-676">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b14e0-677">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b14e0-678">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-678">Option</span></span> | <span data-ttu-id="b14e0-679">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-679">Default Value</span></span> | <span data-ttu-id="b14e0-680">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b14e0-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-681">5 seconds</span></span> | <span data-ttu-id="b14e0-682">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b14e0-683">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b14e0-684">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b14e0-685">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="b14e0-685">Configure client options</span></span>

<span data-ttu-id="b14e0-686">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b14e0-687">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="b14e0-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b14e0-688">registro</span><span class="sxs-lookup"><span data-stu-id="b14e0-688">Configure logging</span></span>

<span data-ttu-id="b14e0-689">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b14e0-690">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b14e0-691">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b14e0-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-692">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="b14e0-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b14e0-693">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b14e0-694">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b14e0-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b14e0-695">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b14e0-696">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="b14e0-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b14e0-697">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="b14e0-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b14e0-698">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b14e0-699">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b14e0-700">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="b14e0-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b14e0-701">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-701">The following table lists the available log levels.</span></span> <span data-ttu-id="b14e0-702">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="b14e0-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b14e0-703">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b14e0-704">String</span><span class="sxs-lookup"><span data-stu-id="b14e0-704">String</span></span>                      | <span data-ttu-id="b14e0-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b14e0-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b14e0-706">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="b14e0-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b14e0-707">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="b14e0-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b14e0-708">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b14e0-709">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b14e0-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b14e0-710">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b14e0-711">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="b14e0-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b14e0-712">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="b14e0-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b14e0-713">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="b14e0-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b14e0-714">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b14e0-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b14e0-715">Configure allowed transports</span></span>

<span data-ttu-id="b14e0-716">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b14e0-717">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b14e0-718">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b14e0-718">All transports are enabled by default.</span></span>

<span data-ttu-id="b14e0-719">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="b14e0-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b14e0-720">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b14e0-721">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="b14e0-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b14e0-722">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b14e0-723">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b14e0-724">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="b14e0-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b14e0-725">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="b14e0-725">Configure bearer authentication</span></span>

<span data-ttu-id="b14e0-726">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b14e0-727">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b14e0-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b14e0-728">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b14e0-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b14e0-729">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b14e0-730">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b14e0-731">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b14e0-732">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b14e0-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b14e0-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b14e0-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b14e0-734">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b14e0-735">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="b14e0-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b14e0-736">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-737">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-738">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-738">Option</span></span> | <span data-ttu-id="b14e0-739">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-739">Default value</span></span> | <span data-ttu-id="b14e0-740">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b14e0-741">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-742">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-742">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-743">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-744">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-745">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-746">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-746">15 seconds</span></span> | <span data-ttu-id="b14e0-747">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-748">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-749">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-750">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-751">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-751">15 seconds</span></span> | <span data-ttu-id="b14e0-752">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-753">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-754">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b14e0-755">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="b14e0-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b14e0-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-757">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-757">Option</span></span> | <span data-ttu-id="b14e0-758">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-758">Default value</span></span> | <span data-ttu-id="b14e0-759">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b14e0-760">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-761">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-761">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-762">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b14e0-763">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-764">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b14e0-765">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-766">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-767">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-768">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-769">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-769">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-770">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-770">Option</span></span> | <span data-ttu-id="b14e0-771">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-771">Default value</span></span> | <span data-ttu-id="b14e0-772">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b14e0-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b14e0-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b14e0-774">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-775">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-775">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-776">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-777">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-778">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b14e0-779">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-779">15 seconds</span></span> | <span data-ttu-id="b14e0-780">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-781">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-782">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-783">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b14e0-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b14e0-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b14e0-785">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-786">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-787">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-788">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b14e0-789">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-789">Configure additional options</span></span>

<span data-ttu-id="b14e0-790">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="b14e0-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-791">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-792">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-792">.NET Option</span></span> |  <span data-ttu-id="b14e0-793">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-793">Default value</span></span> | <span data-ttu-id="b14e0-794">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b14e0-795">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b14e0-796">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-797">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-798">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b14e0-799">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-799">Empty</span></span> | <span data-ttu-id="b14e0-800">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b14e0-801">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-801">Empty</span></span> | <span data-ttu-id="b14e0-802">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b14e0-803">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-803">Empty</span></span> | <span data-ttu-id="b14e0-804">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b14e0-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-805">5 seconds</span></span> | <span data-ttu-id="b14e0-806">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-806">WebSockets only.</span></span> <span data-ttu-id="b14e0-807">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="b14e0-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b14e0-808">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b14e0-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b14e0-809">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-809">Empty</span></span> | <span data-ttu-id="b14e0-810">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b14e0-811">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b14e0-812">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="b14e0-813">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b14e0-814">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b14e0-815">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="b14e0-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b14e0-816">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b14e0-817">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b14e0-818">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="b14e0-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b14e0-819">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b14e0-820">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="b14e0-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b14e0-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-822">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-822">JavaScript Option</span></span> | <span data-ttu-id="b14e0-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-823">Default Value</span></span> | <span data-ttu-id="b14e0-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b14e0-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b14e0-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b14e0-827">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b14e0-828">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-829">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-830">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-831">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-831">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-832">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-832">Java Option</span></span> | <span data-ttu-id="b14e0-833">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-833">Default Value</span></span> | <span data-ttu-id="b14e0-834">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b14e0-835">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b14e0-836">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-837">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-838">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b14e0-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b14e0-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b14e0-840">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-840">Empty</span></span> | <span data-ttu-id="b14e0-841">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b14e0-842">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b14e0-843">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b14e0-844">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b14e0-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b14e0-845">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b14e0-846">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-847">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b14e0-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b14e0-848">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="b14e0-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b14e0-849">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b14e0-850">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b14e0-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b14e0-851">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="b14e0-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b14e0-852">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b14e0-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b14e0-853">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b14e0-854">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b14e0-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b14e0-855">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b14e0-856">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b14e0-857">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-857">MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-858">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b14e0-859">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-860">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b14e0-861">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="b14e0-861">Configure server options</span></span>

<span data-ttu-id="b14e0-862">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="b14e0-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b14e0-863">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-863">Option</span></span> | <span data-ttu-id="b14e0-864">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-864">Default Value</span></span> | <span data-ttu-id="b14e0-865">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b14e0-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-866">30 seconds</span></span> | <span data-ttu-id="b14e0-867">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b14e0-868">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b14e0-869">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b14e0-870">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-870">15 seconds</span></span> | <span data-ttu-id="b14e0-871">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="b14e0-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b14e0-872">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-873">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-874">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-874">15 seconds</span></span> | <span data-ttu-id="b14e0-875">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b14e0-876">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b14e0-877">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b14e0-878">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b14e0-878">All installed protocols</span></span> | <span data-ttu-id="b14e0-879">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-879">Protocols supported by this hub.</span></span> <span data-ttu-id="b14e0-880">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="b14e0-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b14e0-881">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b14e0-882">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="b14e0-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b14e0-883">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b14e0-884">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b14e0-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b14e0-885">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="b14e0-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b14e0-886">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b14e0-887">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b14e0-888">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b14e0-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b14e0-889">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-889">Option</span></span> | <span data-ttu-id="b14e0-890">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-890">Default Value</span></span> | <span data-ttu-id="b14e0-891">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b14e0-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-892">32 KB</span></span> | <span data-ttu-id="b14e0-893">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="b14e0-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b14e0-894">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b14e0-895">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="b14e0-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b14e0-896">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b14e0-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-897">32 KB</span></span> | <span data-ttu-id="b14e0-898">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="b14e0-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b14e0-899">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b14e0-900">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-900">All Transports are enabled.</span></span> | <span data-ttu-id="b14e0-901">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="b14e0-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b14e0-902">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-902">See below.</span></span> | <span data-ttu-id="b14e0-903">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b14e0-904">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-904">See below.</span></span> | <span data-ttu-id="b14e0-905">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b14e0-906">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b14e0-907">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-907">Option</span></span> | <span data-ttu-id="b14e0-908">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-908">Default Value</span></span> | <span data-ttu-id="b14e0-909">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b14e0-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-910">90 seconds</span></span> | <span data-ttu-id="b14e0-911">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b14e0-912">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b14e0-913">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b14e0-914">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-914">Option</span></span> | <span data-ttu-id="b14e0-915">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-915">Default Value</span></span> | <span data-ttu-id="b14e0-916">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b14e0-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-917">5 seconds</span></span> | <span data-ttu-id="b14e0-918">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b14e0-919">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b14e0-920">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b14e0-921">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="b14e0-921">Configure client options</span></span>

<span data-ttu-id="b14e0-922">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b14e0-923">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="b14e0-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b14e0-924">registro</span><span class="sxs-lookup"><span data-stu-id="b14e0-924">Configure logging</span></span>

<span data-ttu-id="b14e0-925">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b14e0-926">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b14e0-927">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b14e0-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-928">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="b14e0-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b14e0-929">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b14e0-930">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b14e0-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b14e0-931">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b14e0-932">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="b14e0-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b14e0-933">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="b14e0-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b14e0-934">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b14e0-935">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b14e0-936">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b14e0-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b14e0-937">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b14e0-938">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="b14e0-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b14e0-939">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="b14e0-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b14e0-940">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="b14e0-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b14e0-941">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b14e0-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b14e0-942">Configure allowed transports</span></span>

<span data-ttu-id="b14e0-943">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b14e0-944">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b14e0-945">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b14e0-945">All transports are enabled by default.</span></span>

<span data-ttu-id="b14e0-946">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="b14e0-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b14e0-947">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b14e0-948">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="b14e0-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b14e0-949">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="b14e0-949">Configure bearer authentication</span></span>

<span data-ttu-id="b14e0-950">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b14e0-951">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b14e0-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b14e0-952">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b14e0-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b14e0-953">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b14e0-954">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b14e0-955">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b14e0-956">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b14e0-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b14e0-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b14e0-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b14e0-958">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b14e0-959">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="b14e0-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b14e0-960">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-961">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-962">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-962">Option</span></span> | <span data-ttu-id="b14e0-963">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-963">Default value</span></span> | <span data-ttu-id="b14e0-964">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b14e0-965">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-966">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-966">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-967">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-968">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-969">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-970">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-970">15 seconds</span></span> | <span data-ttu-id="b14e0-971">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-972">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-973">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-974">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-975">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-975">15 seconds</span></span> | <span data-ttu-id="b14e0-976">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-977">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-978">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b14e0-979">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="b14e0-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b14e0-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-981">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-981">Option</span></span> | <span data-ttu-id="b14e0-982">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-982">Default value</span></span> | <span data-ttu-id="b14e0-983">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b14e0-984">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-985">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-985">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-986">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b14e0-987">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-988">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b14e0-989">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-990">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-991">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-992">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-993">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-993">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-994">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-994">Option</span></span> | <span data-ttu-id="b14e0-995">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-995">Default value</span></span> | <span data-ttu-id="b14e0-996">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b14e0-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b14e0-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b14e0-998">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-999">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-999">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-1000">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-1001">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-1002">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b14e0-1003">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1003">15 seconds</span></span> | <span data-ttu-id="b14e0-1004">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-1005">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-1006">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-1007">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b14e0-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b14e0-1009">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-1010">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b14e0-1011">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b14e0-1012">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b14e0-1013">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-1013">Configure additional options</span></span>

<span data-ttu-id="b14e0-1014">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-1016">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-1016">.NET Option</span></span> |  <span data-ttu-id="b14e0-1017">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1017">Default value</span></span> | <span data-ttu-id="b14e0-1018">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b14e0-1019">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b14e0-1020">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1021">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1022">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b14e0-1023">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1023">Empty</span></span> | <span data-ttu-id="b14e0-1024">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b14e0-1025">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1025">Empty</span></span> | <span data-ttu-id="b14e0-1026">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b14e0-1027">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1027">Empty</span></span> | <span data-ttu-id="b14e0-1028">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b14e0-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1029">5 seconds</span></span> | <span data-ttu-id="b14e0-1030">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1030">WebSockets only.</span></span> <span data-ttu-id="b14e0-1031">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b14e0-1032">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b14e0-1033">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1033">Empty</span></span> | <span data-ttu-id="b14e0-1034">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b14e0-1035">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b14e0-1036">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="b14e0-1037">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b14e0-1038">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b14e0-1039">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="b14e0-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b14e0-1040">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b14e0-1041">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b14e0-1042">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b14e0-1043">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b14e0-1044">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b14e0-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-1046">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-1046">JavaScript Option</span></span> | <span data-ttu-id="b14e0-1047">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1047">Default Value</span></span> | <span data-ttu-id="b14e0-1048">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b14e0-1049">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b14e0-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b14e0-1051">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b14e0-1052">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1053">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1054">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-1055">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-1056">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-1056">Java Option</span></span> | <span data-ttu-id="b14e0-1057">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1057">Default Value</span></span> | <span data-ttu-id="b14e0-1058">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b14e0-1059">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b14e0-1060">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1061">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1062">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b14e0-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b14e0-1064">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1064">Empty</span></span> | <span data-ttu-id="b14e0-1065">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b14e0-1066">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b14e0-1067">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b14e0-1068">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b14e0-1069">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b14e0-1070">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-1071">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b14e0-1072">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b14e0-1073">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b14e0-1074">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b14e0-1075">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b14e0-1076">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b14e0-1077">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b14e0-1078">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b14e0-1079">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b14e0-1080">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b14e0-1081">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="b14e0-1081">MessagePack serialization options</span></span>

<span data-ttu-id="b14e0-1082">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b14e0-1083">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-1084">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b14e0-1085">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="b14e0-1085">Configure server options</span></span>

<span data-ttu-id="b14e0-1086">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b14e0-1087">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1087">Option</span></span> | <span data-ttu-id="b14e0-1088">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1088">Default Value</span></span> | <span data-ttu-id="b14e0-1089">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-1090">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1090">15 seconds</span></span> | <span data-ttu-id="b14e0-1091">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b14e0-1092">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-1093">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b14e0-1094">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1094">15 seconds</span></span> | <span data-ttu-id="b14e0-1095">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b14e0-1096">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b14e0-1097">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b14e0-1098">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b14e0-1098">All installed protocols</span></span> | <span data-ttu-id="b14e0-1099">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="b14e0-1100">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b14e0-1101">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b14e0-1102">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b14e0-1103">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b14e0-1104">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b14e0-1105">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="b14e0-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b14e0-1106">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b14e0-1107">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b14e0-1108">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b14e0-1109">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1109">Option</span></span> | <span data-ttu-id="b14e0-1110">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1110">Default Value</span></span> | <span data-ttu-id="b14e0-1111">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b14e0-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-1112">32 KB</span></span> | <span data-ttu-id="b14e0-1113">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b14e0-1114">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b14e0-1115">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b14e0-1116">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b14e0-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="b14e0-1117">32 KB</span></span> | <span data-ttu-id="b14e0-1118">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b14e0-1119">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b14e0-1120">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1120">All Transports are enabled.</span></span> | <span data-ttu-id="b14e0-1121">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b14e0-1122">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1122">See below.</span></span> | <span data-ttu-id="b14e0-1123">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b14e0-1124">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1124">See below.</span></span> | <span data-ttu-id="b14e0-1125">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b14e0-1126">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b14e0-1127">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1127">Option</span></span> | <span data-ttu-id="b14e0-1128">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1128">Default Value</span></span> | <span data-ttu-id="b14e0-1129">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b14e0-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1130">90 seconds</span></span> | <span data-ttu-id="b14e0-1131">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b14e0-1132">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b14e0-1133">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b14e0-1134">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1134">Option</span></span> | <span data-ttu-id="b14e0-1135">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1135">Default Value</span></span> | <span data-ttu-id="b14e0-1136">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b14e0-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1137">5 seconds</span></span> | <span data-ttu-id="b14e0-1138">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b14e0-1139">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b14e0-1140">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b14e0-1141">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="b14e0-1141">Configure client options</span></span>

<span data-ttu-id="b14e0-1142">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b14e0-1143">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b14e0-1144">registro</span><span class="sxs-lookup"><span data-stu-id="b14e0-1144">Configure logging</span></span>

<span data-ttu-id="b14e0-1145">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b14e0-1146">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b14e0-1147">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b14e0-1148">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b14e0-1149">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b14e0-1150">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b14e0-1151">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b14e0-1152">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b14e0-1153">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b14e0-1154">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b14e0-1155">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b14e0-1156">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b14e0-1157">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b14e0-1158">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b14e0-1159">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b14e0-1160">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b14e0-1161">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b14e0-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1162">Configure allowed transports</span></span>

<span data-ttu-id="b14e0-1163">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b14e0-1164">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b14e0-1165">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="b14e0-1166">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b14e0-1167">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b14e0-1168">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="b14e0-1168">Configure bearer authentication</span></span>

<span data-ttu-id="b14e0-1169">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b14e0-1170">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b14e0-1171">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b14e0-1172">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b14e0-1173">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b14e0-1174">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b14e0-1175">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b14e0-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b14e0-1177">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b14e0-1178">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="b14e0-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b14e0-1179">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-1181">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1181">Option</span></span> | <span data-ttu-id="b14e0-1182">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1182">Default value</span></span> | <span data-ttu-id="b14e0-1183">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b14e0-1184">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-1185">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1185">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-1186">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-1187">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-1188">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b14e0-1189">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1189">15 seconds</span></span> | <span data-ttu-id="b14e0-1190">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-1191">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b14e0-1192">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-1193">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b14e0-1194">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b14e0-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-1196">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1196">Option</span></span> | <span data-ttu-id="b14e0-1197">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1197">Default value</span></span> | <span data-ttu-id="b14e0-1198">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b14e0-1199">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-1200">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1200">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-1201">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b14e0-1202">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-1203">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-1204">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-1205">Opción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1205">Option</span></span> | <span data-ttu-id="b14e0-1206">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1206">Default value</span></span> | <span data-ttu-id="b14e0-1207">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b14e0-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b14e0-1209">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="b14e0-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b14e0-1210">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1210">Timeout for server activity.</span></span> <span data-ttu-id="b14e0-1211">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-1212">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b14e0-1213">El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b14e0-1214">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1214">15 seconds</span></span> | <span data-ttu-id="b14e0-1215">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="b14e0-1216">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b14e0-1217">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b14e0-1218">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b14e0-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b14e0-1219">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-1219">Configure additional options</span></span>

<span data-ttu-id="b14e0-1220">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="b14e0-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b14e0-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b14e0-1222">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="b14e0-1222">.NET Option</span></span> |  <span data-ttu-id="b14e0-1223">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1223">Default value</span></span> | <span data-ttu-id="b14e0-1224">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b14e0-1225">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b14e0-1226">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1227">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1228">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b14e0-1229">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1229">Empty</span></span> | <span data-ttu-id="b14e0-1230">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b14e0-1231">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1231">Empty</span></span> | <span data-ttu-id="b14e0-1232">Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b14e0-1233">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1233">Empty</span></span> | <span data-ttu-id="b14e0-1234">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b14e0-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b14e0-1235">5 seconds</span></span> | <span data-ttu-id="b14e0-1236">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1236">WebSockets only.</span></span> <span data-ttu-id="b14e0-1237">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b14e0-1238">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b14e0-1239">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1239">Empty</span></span> | <span data-ttu-id="b14e0-1240">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b14e0-1241">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b14e0-1242">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="b14e0-1243">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b14e0-1244">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b14e0-1245">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="b14e0-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b14e0-1246">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b14e0-1247">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b14e0-1248">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b14e0-1249">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b14e0-1250">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b14e0-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b14e0-1252">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b14e0-1252">JavaScript Option</span></span> | <span data-ttu-id="b14e0-1253">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1253">Default Value</span></span> | <span data-ttu-id="b14e0-1254">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b14e0-1255">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b14e0-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b14e0-1257">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b14e0-1258">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1259">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1260">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b14e0-1261">Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="b14e0-1262">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="b14e0-1262">Java Option</span></span> | <span data-ttu-id="b14e0-1263">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="b14e0-1263">Default Value</span></span> | <span data-ttu-id="b14e0-1264">Descripción</span><span class="sxs-lookup"><span data-stu-id="b14e0-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b14e0-1265">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b14e0-1266">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b14e0-1267">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b14e0-1268">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b14e0-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b14e0-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b14e0-1270">Vacío</span><span class="sxs-lookup"><span data-stu-id="b14e0-1270">Empty</span></span> | <span data-ttu-id="b14e0-1271">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="b14e0-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b14e0-1272">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b14e0-1273">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b14e0-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b14e0-1274">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b14e0-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b14e0-1275">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e0-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
