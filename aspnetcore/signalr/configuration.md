---
title: 'Configuración de :::no-loc(SignalR)::: en ASP.NET Core'
author: bradygaster
description: 'Aprenda a configurar ASP.NET Core :::no-loc(SignalR)::: aplicaciones.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061241"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="65bb3-103">Configuración de :::no-loc(SignalR)::: en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65bb3-103">ASP.NET Core :::no-loc(SignalR)::: configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="65bb3-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-105">ASP.NET Core :::no-loc(SignalR)::: admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="65bb3-105">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="65bb3-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="65bb3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="65bb3-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="65bb3-108">`AddJsonProtocol`se puede agregar después [de :::no-loc(SignalR)::: Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-108">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65bb3-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="65bb3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="65bb3-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="65bb3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="65bb3-111">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="65bb3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="65bb3-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="65bb3-113">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="65bb3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="65bb3-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="65bb3-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="65bb3-116">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="65bb3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="65bb3-117">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="65bb3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="65bb3-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-118">MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="65bb3-120">Vea [MessagePack en :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-120">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="65bb3-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="65bb3-122">Configure server options</span></span>

<span data-ttu-id="65bb3-123">En la tabla siguiente se describen las opciones para configurar los :::no-loc(SignalR)::: concentradores:</span><span class="sxs-lookup"><span data-stu-id="65bb3-123">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="65bb3-124">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-124">Option</span></span> | <span data-ttu-id="65bb3-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-125">Default Value</span></span> | <span data-ttu-id="65bb3-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="65bb3-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-127">30 seconds</span></span> | <span data-ttu-id="65bb3-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="65bb3-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="65bb3-130">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="65bb3-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-131">15 seconds</span></span> | <span data-ttu-id="65bb3-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="65bb3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="65bb3-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-134">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-134">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-135">15 seconds</span></span> | <span data-ttu-id="65bb3-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="65bb3-137">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="65bb3-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="65bb3-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="65bb3-139">All installed protocols</span></span> | <span data-ttu-id="65bb3-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="65bb3-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="65bb3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="65bb3-142">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="65bb3-143">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="65bb3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="65bb3-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="65bb3-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="65bb3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-146">32 KB</span></span> | <span data-ttu-id="65bb3-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="65bb3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="65bb3-148">1</span><span class="sxs-lookup"><span data-stu-id="65bb3-148">1</span></span> | <span data-ttu-id="65bb3-149">Número máximo de métodos de concentrador a los que cada cliente puede llamar en paralelo antes de poner en cola.</span><span class="sxs-lookup"><span data-stu-id="65bb3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="65bb3-150">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `Add:::no-loc(SignalR):::` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-150">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="65bb3-151">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `Add:::no-loc(SignalR):::` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="65bb3-151">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="65bb3-152">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="65bb3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="65bb3-153">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="65bb3-154">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="65bb3-155">En la tabla siguiente se describen las opciones para configurar :::no-loc(SignalR)::: las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="65bb3-155">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="65bb3-156">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-156">Option</span></span> | <span data-ttu-id="65bb3-157">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-157">Default Value</span></span> | <span data-ttu-id="65bb3-158">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="65bb3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-159">32 KB</span></span> | <span data-ttu-id="65bb3-160">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="65bb3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="65bb3-161">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="65bb3-162">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="65bb3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="65bb3-163">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="65bb3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-164">32 KB</span></span> | <span data-ttu-id="65bb3-165">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="65bb3-166">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="65bb3-167">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-167">All Transports are enabled.</span></span> | <span data-ttu-id="65bb3-168">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="65bb3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="65bb3-169">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-169">See below.</span></span> | <span data-ttu-id="65bb3-170">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="65bb3-171">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-171">See below.</span></span> | <span data-ttu-id="65bb3-172">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="65bb3-173">0</span><span class="sxs-lookup"><span data-stu-id="65bb3-173">0</span></span> | <span data-ttu-id="65bb3-174">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="65bb3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="65bb3-175">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="65bb3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="65bb3-176">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="65bb3-177">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-177">Option</span></span> | <span data-ttu-id="65bb3-178">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-178">Default Value</span></span> | <span data-ttu-id="65bb3-179">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="65bb3-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-180">90 seconds</span></span> | <span data-ttu-id="65bb3-181">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="65bb3-182">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="65bb3-183">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="65bb3-184">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-184">Option</span></span> | <span data-ttu-id="65bb3-185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-185">Default Value</span></span> | <span data-ttu-id="65bb3-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="65bb3-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-187">5 seconds</span></span> | <span data-ttu-id="65bb3-188">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="65bb3-189">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="65bb3-190">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="65bb3-191">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="65bb3-191">Configure client options</span></span>

<span data-ttu-id="65bb3-192">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="65bb3-193">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="65bb3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="65bb3-194">registro</span><span class="sxs-lookup"><span data-stu-id="65bb3-194">Configure logging</span></span>

<span data-ttu-id="65bb3-195">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="65bb3-196">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="65bb3-197">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="65bb3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-198">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="65bb3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="65bb3-199">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="65bb3-200">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="65bb3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="65bb3-201">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="65bb3-202">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="65bb3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="65bb3-203">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="65bb3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="65bb3-204">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="65bb3-205">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="65bb3-206">Esto resulta útil cuando se configura :::no-loc(SignalR)::: el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="65bb3-206">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="65bb3-207">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="65bb3-208">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="65bb3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="65bb3-209">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="65bb3-210">String</span><span class="sxs-lookup"><span data-stu-id="65bb3-210">String</span></span>                      | <span data-ttu-id="65bb3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="65bb3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="65bb3-212">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="65bb3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="65bb3-213">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="65bb3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="65bb3-214">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="65bb3-215">Para obtener más información sobre el registro, consulte la [ :::no-loc(SignalR)::: documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="65bb3-215">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="65bb3-216">El :::no-loc(SignalR)::: cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-216">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="65bb3-217">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="65bb3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="65bb3-218">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el :::no-loc(SignalR)::: cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="65bb3-218">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="65bb3-219">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="65bb3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="65bb3-220">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="65bb3-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="65bb3-221">Configure allowed transports</span></span>

<span data-ttu-id="65bb3-222">Los transportes utilizados por :::no-loc(SignalR)::: pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-222">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="65bb3-223">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="65bb3-224">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="65bb3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="65bb3-225">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="65bb3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="65bb3-226">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="65bb3-227">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="65bb3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="65bb3-228">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="65bb3-229">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="65bb3-230">El :::no-loc(SignalR)::: cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="65bb3-230">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="65bb3-231">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="65bb3-231">Configure bearer authentication</span></span>

<span data-ttu-id="65bb3-232">Para proporcionar datos de autenticación junto con :::no-loc(SignalR)::: las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-232">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="65bb3-233">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="65bb3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="65bb3-234">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="65bb3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="65bb3-235">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="65bb3-236">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="65bb3-237">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="65bb3-238">En el :::no-loc(SignalR)::: cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="65bb3-238">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="65bb3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="65bb3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="65bb3-240">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="65bb3-241">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="65bb3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="65bb3-242">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-244">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-244">Option</span></span> | <span data-ttu-id="65bb3-245">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-245">Default value</span></span> | <span data-ttu-id="65bb3-246">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="65bb3-247">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-248">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-248">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-249">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-250">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-251">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-252">15 seconds</span></span> | <span data-ttu-id="65bb3-253">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-254">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-255">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-256">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-256">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-257">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-257">15 seconds</span></span> | <span data-ttu-id="65bb3-258">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-259">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-260">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="65bb3-261">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="65bb3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="65bb3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-263">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-263">Option</span></span> | <span data-ttu-id="65bb3-264">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-264">Default value</span></span> | <span data-ttu-id="65bb3-265">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="65bb3-266">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-267">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-267">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-268">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="65bb3-269">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-270">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="65bb3-271">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-272">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-273">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-274">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-275">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-276">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-276">Option</span></span> | <span data-ttu-id="65bb3-277">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-277">Default value</span></span> | <span data-ttu-id="65bb3-278">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="65bb3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="65bb3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="65bb3-280">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-281">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-281">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-282">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-283">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-284">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="65bb3-285">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-285">15 seconds</span></span> | <span data-ttu-id="65bb3-286">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-287">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-288">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-289">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-289">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="65bb3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="65bb3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="65bb3-291">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-292">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-293">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-294">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="65bb3-295">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-295">Configure additional options</span></span>

<span data-ttu-id="65bb3-296">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="65bb3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-298">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-298">.NET Option</span></span> |  <span data-ttu-id="65bb3-299">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-299">Default value</span></span> | <span data-ttu-id="65bb3-300">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="65bb3-301">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="65bb3-302">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-303">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-304">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-304">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="65bb3-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-305">Empty</span></span> | <span data-ttu-id="65bb3-306">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="65bb3-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-307">Empty</span></span> | <span data-ttu-id="65bb3-308">Colección de HTTP :::no-loc(cookie)::: s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-308">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="65bb3-309">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-309">Empty</span></span> | <span data-ttu-id="65bb3-310">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="65bb3-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-311">5 seconds</span></span> | <span data-ttu-id="65bb3-312">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-312">WebSockets only.</span></span> <span data-ttu-id="65bb3-313">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="65bb3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="65bb3-314">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65bb3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="65bb3-315">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-315">Empty</span></span> | <span data-ttu-id="65bb3-316">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="65bb3-317">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="65bb3-318">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="65bb3-319">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="65bb3-320">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="65bb3-321">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los :::no-loc(Cookie)::: encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="65bb3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="65bb3-322">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="65bb3-323">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="65bb3-324">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="65bb3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="65bb3-325">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="65bb3-326">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="65bb3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="65bb3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-328">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-328">JavaScript Option</span></span> | <span data-ttu-id="65bb3-329">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-329">Default Value</span></span> | <span data-ttu-id="65bb3-330">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="65bb3-331">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="65bb3-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="65bb3-333">Diccionario de encabezados enviados con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="65bb3-334">El envío de encabezados en el explorador no funciona con WebSockets ni con el <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flujo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="65bb3-335">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="65bb3-336">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-337">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-338">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-338">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="65bb3-339">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="65bb3-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="65bb3-340">Azure App Service usa :::no-loc(cookie)::: para las sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-340">Azure App Service uses :::no-loc(cookie):::s for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="65bb3-341">Para obtener más información sobre CORS con :::no-loc(SignalR)::: , vea <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="65bb3-341">For more info on CORS with :::no-loc(SignalR):::, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-342">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-342">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-343">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-343">Java Option</span></span> | <span data-ttu-id="65bb3-344">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-344">Default Value</span></span> | <span data-ttu-id="65bb3-345">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="65bb3-346">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="65bb3-347">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-348">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-349">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-349">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="65bb3-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="65bb3-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="65bb3-351">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-351">Empty</span></span> | <span data-ttu-id="65bb3-352">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="65bb3-353">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="65bb3-354">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="65bb3-355">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="65bb3-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="65bb3-356">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="65bb3-357">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-358">ASP.NET Core :::no-loc(SignalR)::: admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="65bb3-358">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="65bb3-359">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="65bb3-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="65bb3-360">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="65bb3-361">`AddJsonProtocol`se puede agregar después [de :::no-loc(SignalR)::: Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-361">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65bb3-362">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="65bb3-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="65bb3-363">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="65bb3-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="65bb3-364">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="65bb3-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="65bb3-365">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="65bb3-366">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="65bb3-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="65bb3-367">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="65bb3-368">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="65bb3-369">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="65bb3-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="65bb3-370">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="65bb3-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="65bb3-371">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-371">MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-372">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="65bb3-373">Vea [MessagePack en :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-373">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-374">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="65bb3-375">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="65bb3-375">Configure server options</span></span>

<span data-ttu-id="65bb3-376">En la tabla siguiente se describen las opciones para configurar los :::no-loc(SignalR)::: concentradores:</span><span class="sxs-lookup"><span data-stu-id="65bb3-376">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="65bb3-377">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-377">Option</span></span> | <span data-ttu-id="65bb3-378">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-378">Default Value</span></span> | <span data-ttu-id="65bb3-379">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="65bb3-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-380">30 seconds</span></span> | <span data-ttu-id="65bb3-381">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="65bb3-382">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="65bb3-383">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="65bb3-384">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-384">15 seconds</span></span> | <span data-ttu-id="65bb3-385">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="65bb3-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="65bb3-386">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-387">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-387">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-388">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-388">15 seconds</span></span> | <span data-ttu-id="65bb3-389">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="65bb3-390">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="65bb3-391">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="65bb3-392">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="65bb3-392">All installed protocols</span></span> | <span data-ttu-id="65bb3-393">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-393">Protocols supported by this hub.</span></span> <span data-ttu-id="65bb3-394">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="65bb3-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="65bb3-395">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="65bb3-396">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="65bb3-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="65bb3-397">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="65bb3-398">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="65bb3-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-399">32 KB</span></span> | <span data-ttu-id="65bb3-400">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="65bb3-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="65bb3-401">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `Add:::no-loc(SignalR):::` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-401">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="65bb3-402">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `Add:::no-loc(SignalR):::` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="65bb3-402">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="65bb3-403">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="65bb3-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="65bb3-404">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="65bb3-405">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="65bb3-406">En la tabla siguiente se describen las opciones para configurar :::no-loc(SignalR)::: las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="65bb3-406">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="65bb3-407">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-407">Option</span></span> | <span data-ttu-id="65bb3-408">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-408">Default Value</span></span> | <span data-ttu-id="65bb3-409">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="65bb3-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-410">32 KB</span></span> | <span data-ttu-id="65bb3-411">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="65bb3-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="65bb3-412">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="65bb3-413">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="65bb3-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="65bb3-414">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="65bb3-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-415">32 KB</span></span> | <span data-ttu-id="65bb3-416">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="65bb3-417">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="65bb3-418">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-418">All Transports are enabled.</span></span> | <span data-ttu-id="65bb3-419">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="65bb3-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="65bb3-420">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-420">See below.</span></span> | <span data-ttu-id="65bb3-421">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="65bb3-422">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-422">See below.</span></span> | <span data-ttu-id="65bb3-423">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="65bb3-424">0</span><span class="sxs-lookup"><span data-stu-id="65bb3-424">0</span></span> | <span data-ttu-id="65bb3-425">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="65bb3-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="65bb3-426">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="65bb3-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="65bb3-427">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="65bb3-428">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-428">Option</span></span> | <span data-ttu-id="65bb3-429">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-429">Default Value</span></span> | <span data-ttu-id="65bb3-430">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="65bb3-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-431">90 seconds</span></span> | <span data-ttu-id="65bb3-432">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="65bb3-433">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="65bb3-434">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="65bb3-435">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-435">Option</span></span> | <span data-ttu-id="65bb3-436">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-436">Default Value</span></span> | <span data-ttu-id="65bb3-437">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="65bb3-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-438">5 seconds</span></span> | <span data-ttu-id="65bb3-439">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="65bb3-440">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="65bb3-441">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="65bb3-442">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="65bb3-442">Configure client options</span></span>

<span data-ttu-id="65bb3-443">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="65bb3-444">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="65bb3-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="65bb3-445">registro</span><span class="sxs-lookup"><span data-stu-id="65bb3-445">Configure logging</span></span>

<span data-ttu-id="65bb3-446">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="65bb3-447">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="65bb3-448">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="65bb3-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-449">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="65bb3-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="65bb3-450">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="65bb3-451">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="65bb3-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="65bb3-452">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="65bb3-453">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="65bb3-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="65bb3-454">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="65bb3-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="65bb3-455">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="65bb3-456">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="65bb3-457">Esto resulta útil cuando se configura :::no-loc(SignalR)::: el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="65bb3-457">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="65bb3-458">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-458">The following table lists the available log levels.</span></span> <span data-ttu-id="65bb3-459">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="65bb3-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="65bb3-460">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="65bb3-461">String</span><span class="sxs-lookup"><span data-stu-id="65bb3-461">String</span></span>                      | <span data-ttu-id="65bb3-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="65bb3-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="65bb3-463">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="65bb3-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="65bb3-464">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="65bb3-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="65bb3-465">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="65bb3-466">Para obtener más información sobre el registro, consulte la [ :::no-loc(SignalR)::: documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="65bb3-466">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="65bb3-467">El :::no-loc(SignalR)::: cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-467">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="65bb3-468">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="65bb3-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="65bb3-469">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el :::no-loc(SignalR)::: cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="65bb3-469">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="65bb3-470">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="65bb3-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="65bb3-471">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="65bb3-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="65bb3-472">Configure allowed transports</span></span>

<span data-ttu-id="65bb3-473">Los transportes utilizados por :::no-loc(SignalR)::: pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-473">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="65bb3-474">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="65bb3-475">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="65bb3-475">All transports are enabled by default.</span></span>

<span data-ttu-id="65bb3-476">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="65bb3-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="65bb3-477">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="65bb3-478">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="65bb3-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="65bb3-479">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="65bb3-480">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="65bb3-481">El :::no-loc(SignalR)::: cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="65bb3-481">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="65bb3-482">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="65bb3-482">Configure bearer authentication</span></span>

<span data-ttu-id="65bb3-483">Para proporcionar datos de autenticación junto con :::no-loc(SignalR)::: las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-483">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="65bb3-484">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="65bb3-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="65bb3-485">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="65bb3-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="65bb3-486">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="65bb3-487">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="65bb3-488">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="65bb3-489">En el :::no-loc(SignalR)::: cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="65bb3-489">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="65bb3-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="65bb3-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="65bb3-491">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="65bb3-492">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="65bb3-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="65bb3-493">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-494">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-495">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-495">Option</span></span> | <span data-ttu-id="65bb3-496">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-496">Default value</span></span> | <span data-ttu-id="65bb3-497">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="65bb3-498">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-499">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-499">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-500">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-501">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-502">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-503">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-503">15 seconds</span></span> | <span data-ttu-id="65bb3-504">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-505">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-506">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-507">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-507">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-508">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-508">15 seconds</span></span> | <span data-ttu-id="65bb3-509">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-510">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-511">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="65bb3-512">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="65bb3-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="65bb3-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-514">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-514">Option</span></span> | <span data-ttu-id="65bb3-515">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-515">Default value</span></span> | <span data-ttu-id="65bb3-516">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="65bb3-517">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-518">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-518">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-519">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="65bb3-520">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-521">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="65bb3-522">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-523">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-524">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-525">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-526">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-526">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-527">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-527">Option</span></span> | <span data-ttu-id="65bb3-528">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-528">Default value</span></span> | <span data-ttu-id="65bb3-529">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="65bb3-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="65bb3-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="65bb3-531">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-532">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-532">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-533">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-534">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-535">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="65bb3-536">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-536">15 seconds</span></span> | <span data-ttu-id="65bb3-537">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-538">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-539">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-540">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-540">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="65bb3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="65bb3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="65bb3-542">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-543">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-544">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-545">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="65bb3-546">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-546">Configure additional options</span></span>

<span data-ttu-id="65bb3-547">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="65bb3-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-548">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-549">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-549">.NET Option</span></span> |  <span data-ttu-id="65bb3-550">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-550">Default value</span></span> | <span data-ttu-id="65bb3-551">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="65bb3-552">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="65bb3-553">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-554">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-555">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-555">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="65bb3-556">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-556">Empty</span></span> | <span data-ttu-id="65bb3-557">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="65bb3-558">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-558">Empty</span></span> | <span data-ttu-id="65bb3-559">Colección de HTTP :::no-loc(cookie)::: s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-559">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="65bb3-560">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-560">Empty</span></span> | <span data-ttu-id="65bb3-561">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="65bb3-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-562">5 seconds</span></span> | <span data-ttu-id="65bb3-563">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-563">WebSockets only.</span></span> <span data-ttu-id="65bb3-564">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="65bb3-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="65bb3-565">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65bb3-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="65bb3-566">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-566">Empty</span></span> | <span data-ttu-id="65bb3-567">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="65bb3-568">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="65bb3-569">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="65bb3-570">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="65bb3-571">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="65bb3-572">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los :::no-loc(Cookie)::: encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="65bb3-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="65bb3-573">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="65bb3-574">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="65bb3-575">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="65bb3-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="65bb3-576">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="65bb3-577">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="65bb3-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="65bb3-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-579">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-579">JavaScript Option</span></span> | <span data-ttu-id="65bb3-580">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-580">Default Value</span></span> | <span data-ttu-id="65bb3-581">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="65bb3-582">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="65bb3-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="65bb3-584">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="65bb3-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-587">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-587">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-588">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-588">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-589">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-589">Java Option</span></span> | <span data-ttu-id="65bb3-590">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-590">Default Value</span></span> | <span data-ttu-id="65bb3-591">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="65bb3-592">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="65bb3-593">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-594">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-595">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-595">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="65bb3-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="65bb3-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="65bb3-597">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-597">Empty</span></span> | <span data-ttu-id="65bb3-598">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="65bb3-599">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="65bb3-600">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="65bb3-601">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="65bb3-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="65bb3-602">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="65bb3-603">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-604">ASP.NET Core :::no-loc(SignalR)::: admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="65bb3-604">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="65bb3-605">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="65bb3-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="65bb3-606">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="65bb3-607">`AddJsonProtocol`se puede agregar después [de :::no-loc(SignalR)::: Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-607">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65bb3-608">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="65bb3-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="65bb3-609">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="65bb3-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="65bb3-610">Para obtener más información, consulte la [ documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="65bb3-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="65bb3-611">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="65bb3-612">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="65bb3-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="65bb3-613">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="65bb3-614">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="65bb3-615">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="65bb3-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="65bb3-616">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="65bb3-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="65bb3-617">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-617">MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-618">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="65bb3-619">Vea [MessagePack en :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-619">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-620">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="65bb3-621">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="65bb3-621">Configure server options</span></span>

<span data-ttu-id="65bb3-622">En la tabla siguiente se describen las opciones para configurar los :::no-loc(SignalR)::: concentradores:</span><span class="sxs-lookup"><span data-stu-id="65bb3-622">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="65bb3-623">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-623">Option</span></span> | <span data-ttu-id="65bb3-624">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-624">Default Value</span></span> | <span data-ttu-id="65bb3-625">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="65bb3-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-626">30 seconds</span></span> | <span data-ttu-id="65bb3-627">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="65bb3-628">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="65bb3-629">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="65bb3-630">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-630">15 seconds</span></span> | <span data-ttu-id="65bb3-631">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="65bb3-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="65bb3-632">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-633">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-633">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-634">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-634">15 seconds</span></span> | <span data-ttu-id="65bb3-635">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="65bb3-636">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="65bb3-637">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="65bb3-638">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="65bb3-638">All installed protocols</span></span> | <span data-ttu-id="65bb3-639">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-639">Protocols supported by this hub.</span></span> <span data-ttu-id="65bb3-640">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="65bb3-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="65bb3-641">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="65bb3-642">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="65bb3-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="65bb3-643">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="65bb3-644">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="65bb3-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-645">32 KB</span></span> | <span data-ttu-id="65bb3-646">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="65bb3-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="65bb3-647">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `Add:::no-loc(SignalR):::` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-647">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="65bb3-648">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `Add:::no-loc(SignalR):::` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="65bb3-648">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="65bb3-649">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="65bb3-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="65bb3-650">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="65bb3-651">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="65bb3-652">En la tabla siguiente se describen las opciones para configurar :::no-loc(SignalR)::: las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="65bb3-652">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="65bb3-653">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-653">Option</span></span> | <span data-ttu-id="65bb3-654">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-654">Default Value</span></span> | <span data-ttu-id="65bb3-655">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="65bb3-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-656">32 KB</span></span> | <span data-ttu-id="65bb3-657">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="65bb3-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="65bb3-658">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="65bb3-659">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="65bb3-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="65bb3-660">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="65bb3-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-661">32 KB</span></span> | <span data-ttu-id="65bb3-662">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="65bb3-663">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="65bb3-664">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-664">All Transports are enabled.</span></span> | <span data-ttu-id="65bb3-665">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="65bb3-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="65bb3-666">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-666">See below.</span></span> | <span data-ttu-id="65bb3-667">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="65bb3-668">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-668">See below.</span></span> | <span data-ttu-id="65bb3-669">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="65bb3-670">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="65bb3-671">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-671">Option</span></span> | <span data-ttu-id="65bb3-672">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-672">Default Value</span></span> | <span data-ttu-id="65bb3-673">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="65bb3-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-674">90 seconds</span></span> | <span data-ttu-id="65bb3-675">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="65bb3-676">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="65bb3-677">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="65bb3-678">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-678">Option</span></span> | <span data-ttu-id="65bb3-679">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-679">Default Value</span></span> | <span data-ttu-id="65bb3-680">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="65bb3-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-681">5 seconds</span></span> | <span data-ttu-id="65bb3-682">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="65bb3-683">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="65bb3-684">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="65bb3-685">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="65bb3-685">Configure client options</span></span>

<span data-ttu-id="65bb3-686">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="65bb3-687">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="65bb3-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="65bb3-688">registro</span><span class="sxs-lookup"><span data-stu-id="65bb3-688">Configure logging</span></span>

<span data-ttu-id="65bb3-689">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="65bb3-690">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="65bb3-691">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="65bb3-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-692">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="65bb3-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="65bb3-693">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="65bb3-694">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="65bb3-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="65bb3-695">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="65bb3-696">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="65bb3-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="65bb3-697">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="65bb3-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="65bb3-698">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="65bb3-699">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="65bb3-700">Esto resulta útil cuando se configura :::no-loc(SignalR)::: el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="65bb3-700">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="65bb3-701">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-701">The following table lists the available log levels.</span></span> <span data-ttu-id="65bb3-702">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="65bb3-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="65bb3-703">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="65bb3-704">String</span><span class="sxs-lookup"><span data-stu-id="65bb3-704">String</span></span>                      | <span data-ttu-id="65bb3-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="65bb3-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="65bb3-706">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="65bb3-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="65bb3-707">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="65bb3-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="65bb3-708">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="65bb3-709">Para obtener más información sobre el registro, consulte la [ :::no-loc(SignalR)::: documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="65bb3-709">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="65bb3-710">El :::no-loc(SignalR)::: cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-710">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="65bb3-711">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="65bb3-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="65bb3-712">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el :::no-loc(SignalR)::: cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="65bb3-712">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="65bb3-713">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="65bb3-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="65bb3-714">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="65bb3-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="65bb3-715">Configure allowed transports</span></span>

<span data-ttu-id="65bb3-716">Los transportes utilizados por :::no-loc(SignalR)::: pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-716">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="65bb3-717">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="65bb3-718">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="65bb3-718">All transports are enabled by default.</span></span>

<span data-ttu-id="65bb3-719">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="65bb3-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="65bb3-720">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="65bb3-721">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="65bb3-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="65bb3-722">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="65bb3-723">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="65bb3-724">El :::no-loc(SignalR)::: cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="65bb3-724">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="65bb3-725">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="65bb3-725">Configure bearer authentication</span></span>

<span data-ttu-id="65bb3-726">Para proporcionar datos de autenticación junto con :::no-loc(SignalR)::: las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-726">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="65bb3-727">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="65bb3-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="65bb3-728">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="65bb3-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="65bb3-729">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="65bb3-730">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="65bb3-731">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="65bb3-732">En el :::no-loc(SignalR)::: cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="65bb3-732">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="65bb3-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="65bb3-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="65bb3-734">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="65bb3-735">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="65bb3-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="65bb3-736">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-737">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-738">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-738">Option</span></span> | <span data-ttu-id="65bb3-739">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-739">Default value</span></span> | <span data-ttu-id="65bb3-740">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="65bb3-741">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-742">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-742">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-743">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-744">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-745">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-746">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-746">15 seconds</span></span> | <span data-ttu-id="65bb3-747">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-748">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-749">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-750">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-750">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-751">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-751">15 seconds</span></span> | <span data-ttu-id="65bb3-752">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-753">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-754">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="65bb3-755">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="65bb3-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="65bb3-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-757">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-757">Option</span></span> | <span data-ttu-id="65bb3-758">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-758">Default value</span></span> | <span data-ttu-id="65bb3-759">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="65bb3-760">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-761">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-761">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-762">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="65bb3-763">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-764">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="65bb3-765">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-766">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-767">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-768">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-769">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-769">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-770">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-770">Option</span></span> | <span data-ttu-id="65bb3-771">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-771">Default value</span></span> | <span data-ttu-id="65bb3-772">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="65bb3-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="65bb3-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="65bb3-774">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-775">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-775">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-776">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-777">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-778">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="65bb3-779">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-779">15 seconds</span></span> | <span data-ttu-id="65bb3-780">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-781">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-782">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-783">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-783">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="65bb3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="65bb3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="65bb3-785">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-786">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-787">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-788">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="65bb3-789">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-789">Configure additional options</span></span>

<span data-ttu-id="65bb3-790">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="65bb3-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-791">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-792">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-792">.NET Option</span></span> |  <span data-ttu-id="65bb3-793">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-793">Default value</span></span> | <span data-ttu-id="65bb3-794">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="65bb3-795">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="65bb3-796">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-797">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-798">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-798">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="65bb3-799">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-799">Empty</span></span> | <span data-ttu-id="65bb3-800">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="65bb3-801">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-801">Empty</span></span> | <span data-ttu-id="65bb3-802">Colección de HTTP :::no-loc(cookie)::: s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-802">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="65bb3-803">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-803">Empty</span></span> | <span data-ttu-id="65bb3-804">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="65bb3-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-805">5 seconds</span></span> | <span data-ttu-id="65bb3-806">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-806">WebSockets only.</span></span> <span data-ttu-id="65bb3-807">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="65bb3-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="65bb3-808">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65bb3-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="65bb3-809">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-809">Empty</span></span> | <span data-ttu-id="65bb3-810">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="65bb3-811">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="65bb3-812">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="65bb3-813">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="65bb3-814">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="65bb3-815">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los :::no-loc(Cookie)::: encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="65bb3-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="65bb3-816">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="65bb3-817">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="65bb3-818">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="65bb3-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="65bb3-819">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="65bb3-820">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="65bb3-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="65bb3-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-822">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-822">JavaScript Option</span></span> | <span data-ttu-id="65bb3-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-823">Default Value</span></span> | <span data-ttu-id="65bb3-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="65bb3-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="65bb3-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="65bb3-827">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="65bb3-828">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-829">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-830">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-830">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-831">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-831">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-832">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-832">Java Option</span></span> | <span data-ttu-id="65bb3-833">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-833">Default Value</span></span> | <span data-ttu-id="65bb3-834">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="65bb3-835">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="65bb3-836">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-837">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-838">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-838">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="65bb3-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="65bb3-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="65bb3-840">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-840">Empty</span></span> | <span data-ttu-id="65bb3-841">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="65bb3-842">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="65bb3-843">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="65bb3-844">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="65bb3-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="65bb3-845">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="65bb3-846">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-847">ASP.NET Core :::no-loc(SignalR)::: admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="65bb3-847">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="65bb3-848">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="65bb3-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="65bb3-849">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ :::no-loc(SignalR)::: Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="65bb3-850">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="65bb3-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="65bb3-851">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="65bb3-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="65bb3-852">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="65bb3-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="65bb3-853">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="65bb3-854">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="65bb3-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="65bb3-855">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="65bb3-856">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="65bb3-857">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-857">MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-858">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="65bb3-859">Vea [MessagePack en :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-859">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-860">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="65bb3-861">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="65bb3-861">Configure server options</span></span>

<span data-ttu-id="65bb3-862">En la tabla siguiente se describen las opciones para configurar los :::no-loc(SignalR)::: concentradores:</span><span class="sxs-lookup"><span data-stu-id="65bb3-862">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="65bb3-863">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-863">Option</span></span> | <span data-ttu-id="65bb3-864">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-864">Default Value</span></span> | <span data-ttu-id="65bb3-865">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="65bb3-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-866">30 seconds</span></span> | <span data-ttu-id="65bb3-867">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="65bb3-868">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="65bb3-869">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="65bb3-870">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-870">15 seconds</span></span> | <span data-ttu-id="65bb3-871">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="65bb3-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="65bb3-872">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-873">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-873">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-874">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-874">15 seconds</span></span> | <span data-ttu-id="65bb3-875">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="65bb3-876">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="65bb3-877">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="65bb3-878">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="65bb3-878">All installed protocols</span></span> | <span data-ttu-id="65bb3-879">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-879">Protocols supported by this hub.</span></span> <span data-ttu-id="65bb3-880">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="65bb3-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="65bb3-881">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="65bb3-882">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="65bb3-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="65bb3-883">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `Add:::no-loc(SignalR):::` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-883">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="65bb3-884">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `Add:::no-loc(SignalR):::` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="65bb3-884">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="65bb3-885">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="65bb3-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="65bb3-886">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="65bb3-887">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="65bb3-888">En la tabla siguiente se describen las opciones para configurar :::no-loc(SignalR)::: las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="65bb3-888">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="65bb3-889">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-889">Option</span></span> | <span data-ttu-id="65bb3-890">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-890">Default Value</span></span> | <span data-ttu-id="65bb3-891">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="65bb3-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-892">32 KB</span></span> | <span data-ttu-id="65bb3-893">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="65bb3-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="65bb3-894">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="65bb3-895">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="65bb3-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="65bb3-896">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="65bb3-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-897">32 KB</span></span> | <span data-ttu-id="65bb3-898">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="65bb3-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="65bb3-899">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="65bb3-900">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-900">All Transports are enabled.</span></span> | <span data-ttu-id="65bb3-901">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="65bb3-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="65bb3-902">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-902">See below.</span></span> | <span data-ttu-id="65bb3-903">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="65bb3-904">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-904">See below.</span></span> | <span data-ttu-id="65bb3-905">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="65bb3-906">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="65bb3-907">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-907">Option</span></span> | <span data-ttu-id="65bb3-908">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-908">Default Value</span></span> | <span data-ttu-id="65bb3-909">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="65bb3-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-910">90 seconds</span></span> | <span data-ttu-id="65bb3-911">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="65bb3-912">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="65bb3-913">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="65bb3-914">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-914">Option</span></span> | <span data-ttu-id="65bb3-915">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-915">Default Value</span></span> | <span data-ttu-id="65bb3-916">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="65bb3-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-917">5 seconds</span></span> | <span data-ttu-id="65bb3-918">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="65bb3-919">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="65bb3-920">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="65bb3-921">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="65bb3-921">Configure client options</span></span>

<span data-ttu-id="65bb3-922">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="65bb3-923">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="65bb3-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="65bb3-924">registro</span><span class="sxs-lookup"><span data-stu-id="65bb3-924">Configure logging</span></span>

<span data-ttu-id="65bb3-925">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="65bb3-926">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="65bb3-927">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="65bb3-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-928">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="65bb3-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="65bb3-929">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="65bb3-930">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="65bb3-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="65bb3-931">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="65bb3-932">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="65bb3-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="65bb3-933">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="65bb3-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="65bb3-934">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="65bb3-935">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="65bb3-936">Para obtener más información sobre el registro, consulte la [ :::no-loc(SignalR)::: documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="65bb3-936">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="65bb3-937">El :::no-loc(SignalR)::: cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-937">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="65bb3-938">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="65bb3-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="65bb3-939">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el :::no-loc(SignalR)::: cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="65bb3-939">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="65bb3-940">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="65bb3-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="65bb3-941">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="65bb3-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="65bb3-942">Configure allowed transports</span></span>

<span data-ttu-id="65bb3-943">Los transportes utilizados por :::no-loc(SignalR)::: pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-943">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="65bb3-944">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="65bb3-945">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="65bb3-945">All transports are enabled by default.</span></span>

<span data-ttu-id="65bb3-946">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="65bb3-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="65bb3-947">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="65bb3-948">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="65bb3-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="65bb3-949">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="65bb3-949">Configure bearer authentication</span></span>

<span data-ttu-id="65bb3-950">Para proporcionar datos de autenticación junto con :::no-loc(SignalR)::: las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-950">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="65bb3-951">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="65bb3-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="65bb3-952">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="65bb3-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="65bb3-953">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="65bb3-954">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="65bb3-955">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="65bb3-956">En el :::no-loc(SignalR)::: cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="65bb3-956">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="65bb3-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="65bb3-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="65bb3-958">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="65bb3-959">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="65bb3-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="65bb3-960">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-961">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-962">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-962">Option</span></span> | <span data-ttu-id="65bb3-963">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-963">Default value</span></span> | <span data-ttu-id="65bb3-964">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="65bb3-965">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-966">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-966">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-967">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-968">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-969">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-970">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-970">15 seconds</span></span> | <span data-ttu-id="65bb3-971">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-972">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-973">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-974">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-974">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-975">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-975">15 seconds</span></span> | <span data-ttu-id="65bb3-976">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-977">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-978">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="65bb3-979">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="65bb3-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="65bb3-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-981">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-981">Option</span></span> | <span data-ttu-id="65bb3-982">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-982">Default value</span></span> | <span data-ttu-id="65bb3-983">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="65bb3-984">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-985">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-985">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-986">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="65bb3-987">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-988">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="65bb3-989">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-990">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-991">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-992">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-993">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-993">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-994">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-994">Option</span></span> | <span data-ttu-id="65bb3-995">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-995">Default value</span></span> | <span data-ttu-id="65bb3-996">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="65bb3-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="65bb3-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="65bb3-998">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-999">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-999">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-1000">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-1001">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-1002">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="65bb3-1003">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1003">15 seconds</span></span> | <span data-ttu-id="65bb3-1004">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-1005">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-1006">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-1007">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1007">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="65bb3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="65bb3-1009">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-1010">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="65bb3-1011">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="65bb3-1012">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="65bb3-1013">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-1013">Configure additional options</span></span>

<span data-ttu-id="65bb3-1014">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-1016">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-1016">.NET Option</span></span> |  <span data-ttu-id="65bb3-1017">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1017">Default value</span></span> | <span data-ttu-id="65bb3-1018">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="65bb3-1019">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="65bb3-1020">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1021">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1022">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1022">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="65bb3-1023">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1023">Empty</span></span> | <span data-ttu-id="65bb3-1024">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="65bb3-1025">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1025">Empty</span></span> | <span data-ttu-id="65bb3-1026">Colección de HTTP :::no-loc(cookie)::: s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1026">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="65bb3-1027">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1027">Empty</span></span> | <span data-ttu-id="65bb3-1028">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="65bb3-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1029">5 seconds</span></span> | <span data-ttu-id="65bb3-1030">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1030">WebSockets only.</span></span> <span data-ttu-id="65bb3-1031">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="65bb3-1032">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="65bb3-1033">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1033">Empty</span></span> | <span data-ttu-id="65bb3-1034">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="65bb3-1035">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="65bb3-1036">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="65bb3-1037">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="65bb3-1038">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="65bb3-1039">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los :::no-loc(Cookie)::: encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="65bb3-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="65bb3-1040">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="65bb3-1041">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="65bb3-1042">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="65bb3-1043">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="65bb3-1044">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="65bb3-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-1046">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-1046">JavaScript Option</span></span> | <span data-ttu-id="65bb3-1047">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1047">Default Value</span></span> | <span data-ttu-id="65bb3-1048">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="65bb3-1049">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="65bb3-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="65bb3-1051">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="65bb3-1052">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1053">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1054">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1054">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-1055">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-1056">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-1056">Java Option</span></span> | <span data-ttu-id="65bb3-1057">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1057">Default Value</span></span> | <span data-ttu-id="65bb3-1058">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="65bb3-1059">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="65bb3-1060">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1061">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1062">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1062">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="65bb3-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="65bb3-1064">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1064">Empty</span></span> | <span data-ttu-id="65bb3-1065">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="65bb3-1066">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="65bb3-1067">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="65bb3-1068">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="65bb3-1069">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="65bb3-1070">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-1071">ASP.NET Core :::no-loc(SignalR)::: admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1071">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="65bb3-1072">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="65bb3-1073">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ :::no-loc(SignalR)::: Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="65bb3-1074">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="65bb3-1075">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="65bb3-1076">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="65bb3-1077">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="65bb3-1078">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="65bb3-1079">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="65bb3-1080">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="65bb3-1081">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="65bb3-1081">MessagePack serialization options</span></span>

<span data-ttu-id="65bb3-1082">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="65bb3-1083">Vea [MessagePack en :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1083">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-1084">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="65bb3-1085">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="65bb3-1085">Configure server options</span></span>

<span data-ttu-id="65bb3-1086">En la tabla siguiente se describen las opciones para configurar los :::no-loc(SignalR)::: concentradores:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1086">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="65bb3-1087">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1087">Option</span></span> | <span data-ttu-id="65bb3-1088">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1088">Default Value</span></span> | <span data-ttu-id="65bb3-1089">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-1090">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1090">15 seconds</span></span> | <span data-ttu-id="65bb3-1091">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="65bb3-1092">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-1093">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1093">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="65bb3-1094">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1094">15 seconds</span></span> | <span data-ttu-id="65bb3-1095">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="65bb3-1096">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="65bb3-1097">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="65bb3-1098">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="65bb3-1098">All installed protocols</span></span> | <span data-ttu-id="65bb3-1099">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="65bb3-1100">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="65bb3-1101">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="65bb3-1102">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="65bb3-1103">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `Add:::no-loc(SignalR):::` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1103">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="65bb3-1104">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `Add:::no-loc(SignalR):::` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1104">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="65bb3-1105">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="65bb3-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="65bb3-1106">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="65bb3-1107">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="65bb3-1108">En la tabla siguiente se describen las opciones para configurar :::no-loc(SignalR)::: las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1108">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="65bb3-1109">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1109">Option</span></span> | <span data-ttu-id="65bb3-1110">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1110">Default Value</span></span> | <span data-ttu-id="65bb3-1111">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="65bb3-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-1112">32 KB</span></span> | <span data-ttu-id="65bb3-1113">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="65bb3-1114">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="65bb3-1115">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="65bb3-1116">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="65bb3-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="65bb3-1117">32 KB</span></span> | <span data-ttu-id="65bb3-1118">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="65bb3-1119">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="65bb3-1120">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1120">All Transports are enabled.</span></span> | <span data-ttu-id="65bb3-1121">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="65bb3-1122">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1122">See below.</span></span> | <span data-ttu-id="65bb3-1123">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="65bb3-1124">Vea a continuación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1124">See below.</span></span> | <span data-ttu-id="65bb3-1125">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="65bb3-1126">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="65bb3-1127">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1127">Option</span></span> | <span data-ttu-id="65bb3-1128">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1128">Default Value</span></span> | <span data-ttu-id="65bb3-1129">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="65bb3-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1130">90 seconds</span></span> | <span data-ttu-id="65bb3-1131">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="65bb3-1132">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="65bb3-1133">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="65bb3-1134">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1134">Option</span></span> | <span data-ttu-id="65bb3-1135">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1135">Default Value</span></span> | <span data-ttu-id="65bb3-1136">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="65bb3-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1137">5 seconds</span></span> | <span data-ttu-id="65bb3-1138">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="65bb3-1139">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="65bb3-1140">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="65bb3-1141">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="65bb3-1141">Configure client options</span></span>

<span data-ttu-id="65bb3-1142">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="65bb3-1143">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="65bb3-1144">registro</span><span class="sxs-lookup"><span data-stu-id="65bb3-1144">Configure logging</span></span>

<span data-ttu-id="65bb3-1145">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="65bb3-1146">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="65bb3-1147">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="65bb3-1148">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="65bb3-1149">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="65bb3-1150">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="65bb3-1151">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="65bb3-1152">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="65bb3-1153">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="65bb3-1154">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="65bb3-1155">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="65bb3-1156">Para obtener más información sobre el registro, consulte la [ :::no-loc(SignalR)::: documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1156">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="65bb3-1157">El :::no-loc(SignalR)::: cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1157">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="65bb3-1158">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="65bb3-1159">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el :::no-loc(SignalR)::: cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1159">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="65bb3-1160">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="65bb3-1161">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="65bb3-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1162">Configure allowed transports</span></span>

<span data-ttu-id="65bb3-1163">Los transportes utilizados por :::no-loc(SignalR)::: pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1163">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="65bb3-1164">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="65bb3-1165">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="65bb3-1166">Por ejemplo, para deshabilitar el transporte de eventos Server-Sent, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="65bb3-1167">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="65bb3-1168">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="65bb3-1168">Configure bearer authentication</span></span>

<span data-ttu-id="65bb3-1169">Para proporcionar datos de autenticación junto con :::no-loc(SignalR)::: las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1169">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="65bb3-1170">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="65bb3-1171">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (concretamente, en Server-Sent eventos y solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="65bb3-1172">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="65bb3-1173">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="65bb3-1174">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="65bb3-1175">En el :::no-loc(SignalR)::: cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1175">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="65bb3-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="65bb3-1177">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="65bb3-1178">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="65bb3-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="65bb3-1179">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-1181">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1181">Option</span></span> | <span data-ttu-id="65bb3-1182">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1182">Default value</span></span> | <span data-ttu-id="65bb3-1183">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="65bb3-1184">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-1185">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1185">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-1186">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-1187">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-1188">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="65bb3-1189">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1189">15 seconds</span></span> | <span data-ttu-id="65bb3-1190">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-1191">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="65bb3-1192">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-1193">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1193">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="65bb3-1194">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="65bb3-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-1196">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1196">Option</span></span> | <span data-ttu-id="65bb3-1197">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1197">Default value</span></span> | <span data-ttu-id="65bb3-1198">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="65bb3-1199">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-1200">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1200">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-1201">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="65bb3-1202">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-1203">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-1204">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-1205">Opción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1205">Option</span></span> | <span data-ttu-id="65bb3-1206">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1206">Default value</span></span> | <span data-ttu-id="65bb3-1207">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="65bb3-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="65bb3-1209">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="65bb3-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="65bb3-1210">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1210">Timeout for server activity.</span></span> <span data-ttu-id="65bb3-1211">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-1212">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="65bb3-1213">El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="65bb3-1214">15 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1214">15 seconds</span></span> | <span data-ttu-id="65bb3-1215">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="65bb3-1216">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="65bb3-1217">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="65bb3-1218">Para obtener más información sobre el proceso de enlace, consulte la [ :::no-loc(SignalR)::: especificación del Protocolo de concentrador](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="65bb3-1218">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="65bb3-1219">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-1219">Configure additional options</span></span>

<span data-ttu-id="65bb3-1220">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="65bb3-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="65bb3-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="65bb3-1222">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="65bb3-1222">.NET Option</span></span> |  <span data-ttu-id="65bb3-1223">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1223">Default value</span></span> | <span data-ttu-id="65bb3-1224">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="65bb3-1225">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="65bb3-1226">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1227">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1228">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1228">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="65bb3-1229">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1229">Empty</span></span> | <span data-ttu-id="65bb3-1230">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="65bb3-1231">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1231">Empty</span></span> | <span data-ttu-id="65bb3-1232">Colección de HTTP :::no-loc(cookie)::: s que se va a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1232">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="65bb3-1233">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1233">Empty</span></span> | <span data-ttu-id="65bb3-1234">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="65bb3-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="65bb3-1235">5 seconds</span></span> | <span data-ttu-id="65bb3-1236">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1236">WebSockets only.</span></span> <span data-ttu-id="65bb3-1237">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="65bb3-1238">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="65bb3-1239">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1239">Empty</span></span> | <span data-ttu-id="65bb3-1240">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="65bb3-1241">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="65bb3-1242">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="65bb3-1243">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="65bb3-1244">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="65bb3-1245">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los :::no-loc(Cookie)::: encabezados y) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="65bb3-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="65bb3-1246">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="65bb3-1247">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="65bb3-1248">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="65bb3-1249">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="65bb3-1250">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="65bb3-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="65bb3-1252">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="65bb3-1252">JavaScript Option</span></span> | <span data-ttu-id="65bb3-1253">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1253">Default Value</span></span> | <span data-ttu-id="65bb3-1254">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="65bb3-1255">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="65bb3-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valor que especifica el transporte que se va a utilizar para la conexión.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="65bb3-1257">Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="65bb3-1258">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1259">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1260">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1260">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="65bb3-1261">Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="65bb3-1262">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="65bb3-1262">Java Option</span></span> | <span data-ttu-id="65bb3-1263">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="65bb3-1263">Default Value</span></span> | <span data-ttu-id="65bb3-1264">Descripción</span><span class="sxs-lookup"><span data-stu-id="65bb3-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="65bb3-1265">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="65bb3-1266">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="65bb3-1267">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado** .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="65bb3-1268">Esta configuración no se puede habilitar cuando se usa el servicio de Azure :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="65bb3-1268">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="65bb3-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="65bb3-1270">Vacío</span><span class="sxs-lookup"><span data-stu-id="65bb3-1270">Empty</span></span> | <span data-ttu-id="65bb3-1271">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="65bb3-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="65bb3-1272">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="65bb3-1273">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="65bb3-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="65bb3-1274">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="65bb3-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="65bb3-1275">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="65bb3-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
