---
title: Diferencias entre SignalR y ASP.net Core SignalR
author: bradygaster
description: Diferencias entre SignalR y ASP.net Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: a8336a6c13c502f5a0fad150785cd9d484064618
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633011"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="7a36f-103">Diferencias entre ASP.NET SignalR y ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="7a36f-104">ASP.NET Core SignalR no es compatible con clientes o servidores de ASP.net SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="7a36f-105">En este artículo se detallan las características que se han quitado o cambiado en ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="7a36f-106">Cómo identificar la SignalR versión</span><span class="sxs-lookup"><span data-stu-id="7a36f-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="7a36f-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-107">ASP.NET SignalR</span></span> | <span data-ttu-id="7a36f-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="7a36f-109">**Paquete NuGet de servidor**</span><span class="sxs-lookup"><span data-stu-id="7a36f-109">**Server NuGet package**</span></span> | <span data-ttu-id="7a36f-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="7a36f-111">Ninguno.</span><span class="sxs-lookup"><span data-stu-id="7a36f-111">None.</span></span> <span data-ttu-id="7a36f-112">Se incluye en el marco de trabajo compartido [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="7a36f-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="7a36f-113">**Paquetes NuGet de cliente**</span><span class="sxs-lookup"><span data-stu-id="7a36f-113">**Client NuGet packages**</span></span> | <span data-ttu-id="7a36f-114">[Microsoft. AspNet. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="7a36f-115">[Microsoft. AspNet. SignalR . ASPX](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="7a36f-116">[Microsoft. AspNetCore. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="7a36f-117">**Paquete NPM de cliente de JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7a36f-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="7a36f-118">signalr</span><span class="sxs-lookup"><span data-stu-id="7a36f-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="7a36f-119">**Cliente de Java**</span><span class="sxs-lookup"><span data-stu-id="7a36f-119">**Java client**</span></span> | <span data-ttu-id="7a36f-120">[Repositorio de github](https://github.com/SignalR/java-client) (desusado)</span><span class="sxs-lookup"><span data-stu-id="7a36f-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="7a36f-121">Paquete Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="7a36f-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="7a36f-122">**Tipo de aplicación de servidor**</span><span class="sxs-lookup"><span data-stu-id="7a36f-122">**Server app type**</span></span> | <span data-ttu-id="7a36f-123">ASP.NET (System. Web) o Self-host OWIN</span><span class="sxs-lookup"><span data-stu-id="7a36f-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="7a36f-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a36f-124">ASP.NET Core</span></span> |
| <span data-ttu-id="7a36f-125">**Plataformas de servidor admitidas**</span><span class="sxs-lookup"><span data-stu-id="7a36f-125">**Supported server platforms**</span></span> | <span data-ttu-id="7a36f-126">.NET Framework 4,5 o posterior</span><span class="sxs-lookup"><span data-stu-id="7a36f-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="7a36f-127">.NET Core 3,0 o posterior</span><span class="sxs-lookup"><span data-stu-id="7a36f-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="7a36f-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-128">ASP.NET SignalR</span></span> | <span data-ttu-id="7a36f-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="7a36f-130">**Paquete NuGet de servidor**</span><span class="sxs-lookup"><span data-stu-id="7a36f-130">**Server NuGet package**</span></span> | <span data-ttu-id="7a36f-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="7a36f-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="7a36f-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="7a36f-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="7a36f-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="7a36f-134">(.NET Framework)</span></span> |
| <span data-ttu-id="7a36f-135">**Paquetes NuGet de cliente**</span><span class="sxs-lookup"><span data-stu-id="7a36f-135">**Client NuGet packages**</span></span> | <span data-ttu-id="7a36f-136">[Microsoft. AspNet. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="7a36f-137">[Microsoft. AspNet. SignalR . ASPX](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="7a36f-138">[Microsoft. AspNetCore. SignalR . Nº](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="7a36f-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="7a36f-139">**Paquete NPM de cliente de JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7a36f-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="7a36f-140">signalr</span><span class="sxs-lookup"><span data-stu-id="7a36f-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="7a36f-141">**Cliente de Java**</span><span class="sxs-lookup"><span data-stu-id="7a36f-141">**Java client**</span></span> | <span data-ttu-id="7a36f-142">[Repositorio de github](https://github.com/SignalR/java-client) (desusado)</span><span class="sxs-lookup"><span data-stu-id="7a36f-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="7a36f-143">Paquete Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="7a36f-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="7a36f-144">**Tipo de aplicación de servidor**</span><span class="sxs-lookup"><span data-stu-id="7a36f-144">**Server app type**</span></span> | <span data-ttu-id="7a36f-145">ASP.NET (System. Web) o Self-host OWIN</span><span class="sxs-lookup"><span data-stu-id="7a36f-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="7a36f-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a36f-146">ASP.NET Core</span></span> |
| <span data-ttu-id="7a36f-147">**Plataformas de servidor admitidas**</span><span class="sxs-lookup"><span data-stu-id="7a36f-147">**Supported server platforms**</span></span> | <span data-ttu-id="7a36f-148">.NET Framework 4,5 o posterior</span><span class="sxs-lookup"><span data-stu-id="7a36f-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="7a36f-149">.NET Framework 4.6.1 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="7a36f-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="7a36f-150">.NET Core 2,1 o posterior</span><span class="sxs-lookup"><span data-stu-id="7a36f-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="7a36f-151">Diferencias de características</span><span class="sxs-lookup"><span data-stu-id="7a36f-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="7a36f-152">Reconexiones automáticas</span><span class="sxs-lookup"><span data-stu-id="7a36f-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a36f-153">En ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="7a36f-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="7a36f-154">De forma predeterminada, SignalR intenta volver a conectarse al servidor si se quita la conexión.</span><span class="sxs-lookup"><span data-stu-id="7a36f-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="7a36f-155">En ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="7a36f-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="7a36f-156">Las reconexiones automáticas se incluyen en el [cliente de .net](xref:signalr/dotnet-client#automatically-reconnect) y en el [cliente de JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="7a36f-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a36f-157">Antes de ASP.NET Core 3,0, SignalR no es compatible con las reconexiones automáticas.</span><span class="sxs-lookup"><span data-stu-id="7a36f-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="7a36f-158">Si el cliente está desconectado, el usuario debe iniciar explícitamente una nueva conexión para volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="7a36f-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="7a36f-159">En ASP.NET SignalR , SignalR intenta volver a conectarse al servidor si se quita la conexión.</span><span class="sxs-lookup"><span data-stu-id="7a36f-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="7a36f-160">Compatibilidad con protocolos</span><span class="sxs-lookup"><span data-stu-id="7a36f-160">Protocol support</span></span>

<span data-ttu-id="7a36f-161">ASP.NET Core SignalR admite JSON, así como un nuevo protocolo binario basado en [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="7a36f-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="7a36f-162">Además, se pueden crear protocolos personalizados.</span><span class="sxs-lookup"><span data-stu-id="7a36f-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="7a36f-163">Transportes</span><span class="sxs-lookup"><span data-stu-id="7a36f-163">Transports</span></span>

<span data-ttu-id="7a36f-164">El transporte de tramas Forever no se admite en ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="7a36f-165">Diferencias en el servidor</span><span class="sxs-lookup"><span data-stu-id="7a36f-165">Differences on the server</span></span>

<span data-ttu-id="7a36f-166">Las SignalR bibliotecas del lado servidor de ASP.net Core se incluyen en [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que se usa en la plantilla de **aplicación Web ASP.net Core** para los Razor proyectos de y MVC.</span><span class="sxs-lookup"><span data-stu-id="7a36f-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="7a36f-167">ASP.NET Core SignalR es un middleware ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="7a36f-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="7a36f-168">Se debe configurar llamando a <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7a36f-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a36f-169">Para configurar el enrutamiento, asigne rutas a los concentradores dentro de la <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> llamada al método en el `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="7a36f-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a36f-170">Para configurar el enrutamiento, asigne rutas a los concentradores dentro de la <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> llamada al método en el `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="7a36f-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="7a36f-171">Sesiones permanentes</span><span class="sxs-lookup"><span data-stu-id="7a36f-171">Sticky sessions</span></span>

<span data-ttu-id="7a36f-172">El modelo ampliación para ASP.NET SignalR permite a los clientes volver a conectarse y enviar mensajes a cualquier servidor de la granja.</span><span class="sxs-lookup"><span data-stu-id="7a36f-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="7a36f-173">En ASP.NET Core SignalR , el cliente debe interactuar con el mismo servidor mientras dure la conexión.</span><span class="sxs-lookup"><span data-stu-id="7a36f-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="7a36f-174">En el caso de ampliación con Redis, eso significa que se requieren sesiones permanentes.</span><span class="sxs-lookup"><span data-stu-id="7a36f-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="7a36f-175">En el caso de ampliación con el [ SignalR servicio de Azure](/azure/azure-signalr/), las sesiones permanentes no son necesarias porque el servicio administra las conexiones a los clientes.</span><span class="sxs-lookup"><span data-stu-id="7a36f-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="7a36f-176">Un solo concentrador por conexión</span><span class="sxs-lookup"><span data-stu-id="7a36f-176">Single hub per connection</span></span>

<span data-ttu-id="7a36f-177">En ASP.NET Core SignalR , se ha simplificado el modelo de conexión.</span><span class="sxs-lookup"><span data-stu-id="7a36f-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="7a36f-178">Las conexiones se realizan directamente en un solo concentrador, en lugar de usar una sola conexión para compartir el acceso a varios centros.</span><span class="sxs-lookup"><span data-stu-id="7a36f-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="7a36f-179">Streaming</span><span class="sxs-lookup"><span data-stu-id="7a36f-179">Streaming</span></span>

<span data-ttu-id="7a36f-180">ASP.NET Core SignalR ahora admite el [streaming de datos](xref:signalr/streaming) desde el concentrador al cliente.</span><span class="sxs-lookup"><span data-stu-id="7a36f-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="7a36f-181">Estado</span><span class="sxs-lookup"><span data-stu-id="7a36f-181">State</span></span>

<span data-ttu-id="7a36f-182">Se ha quitado la capacidad de pasar el estado arbitrario entre los clientes y el concentrador (a menudo denominado `HubState` ), así como la compatibilidad con los mensajes de progreso.</span><span class="sxs-lookup"><span data-stu-id="7a36f-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="7a36f-183">En este momento no hay ningún homólogo de servidores proxy de concentrador.</span><span class="sxs-lookup"><span data-stu-id="7a36f-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="7a36f-184">Eliminación de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="7a36f-184">PersistentConnection removal</span></span>

<span data-ttu-id="7a36f-185">En ASP.NET Core SignalR , se ha quitado la clase [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .</span><span class="sxs-lookup"><span data-stu-id="7a36f-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="7a36f-186">Host global</span><span class="sxs-lookup"><span data-stu-id="7a36f-186">GlobalHost</span></span>

<span data-ttu-id="7a36f-187">ASP.NET Core tiene la inserción de dependencias (DI) integrada en el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="7a36f-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="7a36f-188">Los servicios pueden usar DI para acceder a [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="7a36f-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="7a36f-189">El `GlobalHost` objeto que se usa en ASP.net SignalR para obtener `HubContext` no existe en ASP.net Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="7a36f-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="7a36f-190">HubPipeline</span></span>

<span data-ttu-id="7a36f-191">ASP.NET Core SignalR no es compatible con los `HubPipeline` módulos de.</span><span class="sxs-lookup"><span data-stu-id="7a36f-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="7a36f-192">Diferencias en el cliente</span><span class="sxs-lookup"><span data-stu-id="7a36f-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="7a36f-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="7a36f-193">TypeScript</span></span>

<span data-ttu-id="7a36f-194">El SignalR cliente de ASP.net Core está escrito en [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="7a36f-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="7a36f-195">Puede escribir en JavaScript o TypeScript cuando se usa el [cliente de JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="7a36f-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="7a36f-196">El cliente de JavaScript se hospeda en NPM</span><span class="sxs-lookup"><span data-stu-id="7a36f-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a36f-197">En las versiones de ASP.NET, el cliente de JavaScript se obtuvo a través de un paquete NuGet en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7a36f-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="7a36f-198">En las versiones ASP.NET Core, el [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) paquete NPM contiene las bibliotecas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7a36f-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="7a36f-199">Este paquete no está incluido en la plantilla de **aplicación Web de ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="7a36f-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="7a36f-200">Use NPM para obtener e instalar el `@microsoft/signalr` paquete NPM.</span><span class="sxs-lookup"><span data-stu-id="7a36f-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a36f-201">En las versiones de ASP.NET, el cliente de JavaScript se obtuvo a través de un paquete NuGet en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7a36f-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="7a36f-202">En las versiones ASP.NET Core, el [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) paquete NPM contiene las bibliotecas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7a36f-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="7a36f-203">Este paquete no está incluido en la plantilla de **aplicación Web de ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="7a36f-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="7a36f-204">Use NPM para obtener e instalar el `@aspnet/signalr` paquete NPM.</span><span class="sxs-lookup"><span data-stu-id="7a36f-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="7a36f-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="7a36f-205">jQuery</span></span>

<span data-ttu-id="7a36f-206">La dependencia de jQuery se ha quitado, pero los proyectos todavía pueden usar jQuery.</span><span class="sxs-lookup"><span data-stu-id="7a36f-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="7a36f-207">Compatibilidad con Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7a36f-207">Internet Explorer support</span></span>

<span data-ttu-id="7a36f-208">ASP.NET Core SignalR requiere Microsoft Internet Explorer 11 o posterior (ASP.net SignalR compatible con Microsoft Internet Explorer 8 y versiones posteriores).</span><span class="sxs-lookup"><span data-stu-id="7a36f-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="7a36f-209">Sintaxis del método de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a36f-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a36f-210">La sintaxis de JavaScript ha cambiado con respecto a la versión de ASP.NET de SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="7a36f-211">En lugar de usar el `$connection` objeto, cree una conexión mediante la API de [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="7a36f-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="7a36f-212">Use el método on para especificar los métodos [de](/javascript/api/@microsoft/signalr/HubConnection#on) cliente a los que puede llamar el concentrador.</span><span class="sxs-lookup"><span data-stu-id="7a36f-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a36f-213">La sintaxis de JavaScript ha cambiado con respecto a la versión de ASP.NET de SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="7a36f-214">En lugar de usar el `$connection` objeto, cree una conexión mediante la API de [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="7a36f-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="7a36f-215">Use el método on para especificar los métodos [de](/javascript/api/@aspnet/signalr/HubConnection#on) cliente a los que puede llamar el concentrador.</span><span class="sxs-lookup"><span data-stu-id="7a36f-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="7a36f-216">Después de crear el método de cliente, inicie la conexión del concentrador.</span><span class="sxs-lookup"><span data-stu-id="7a36f-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="7a36f-217">Encadenar un método [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar o controlar los errores.</span><span class="sxs-lookup"><span data-stu-id="7a36f-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="7a36f-218">Servidores proxy de concentrador</span><span class="sxs-lookup"><span data-stu-id="7a36f-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a36f-219">Los proxies de concentrador ya no se generan automáticamente.</span><span class="sxs-lookup"><span data-stu-id="7a36f-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="7a36f-220">En su lugar, el nombre del método se pasa a la API de [invocación](/javascript/api/@microsoft/signalr/hubconnection#invoke) como una cadena.</span><span class="sxs-lookup"><span data-stu-id="7a36f-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a36f-221">Los proxies de concentrador ya no se generan automáticamente.</span><span class="sxs-lookup"><span data-stu-id="7a36f-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="7a36f-222">En su lugar, el nombre del método se pasa a la API de [invocación](/javascript/api/@aspnet/signalr/hubconnection#invoke) como una cadena.</span><span class="sxs-lookup"><span data-stu-id="7a36f-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="7a36f-223">.NET y otros clientes</span><span class="sxs-lookup"><span data-stu-id="7a36f-223">.NET and other clients</span></span>

<span data-ttu-id="7a36f-224">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)El paquete NuGet de cliente contiene las bibliotecas de cliente de .net para ASP.net Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="7a36f-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="7a36f-225">Utilice <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> para crear y compilar una instancia de una conexión a un concentrador.</span><span class="sxs-lookup"><span data-stu-id="7a36f-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="7a36f-226">Diferencias de ampliación</span><span class="sxs-lookup"><span data-stu-id="7a36f-226">Scaleout differences</span></span>

<span data-ttu-id="7a36f-227">ASP.NET SignalR admite SQL Server y Redis.</span><span class="sxs-lookup"><span data-stu-id="7a36f-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="7a36f-228">ASP.NET Core SignalR admite el SignalR servicio de Azure y Redis.</span><span class="sxs-lookup"><span data-stu-id="7a36f-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="7a36f-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="7a36f-229">ASP.NET</span></span>

* [<span data-ttu-id="7a36f-230">SignalR Ampliación con Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="7a36f-230">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="7a36f-231">SignalR Ampliación con Redis</span><span class="sxs-lookup"><span data-stu-id="7a36f-231">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="7a36f-232">SignalR Ampliación con SQL Server</span><span class="sxs-lookup"><span data-stu-id="7a36f-232">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="7a36f-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a36f-233">ASP.NET Core</span></span>

* [<span data-ttu-id="7a36f-234">Servicio de Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="7a36f-234">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="7a36f-235">Backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="7a36f-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="7a36f-236">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7a36f-236">Additional resources</span></span>

* [<span data-ttu-id="7a36f-237">Concentradores</span><span class="sxs-lookup"><span data-stu-id="7a36f-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7a36f-238">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a36f-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="7a36f-239">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="7a36f-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7a36f-240">Plataformas compatibles</span><span class="sxs-lookup"><span data-stu-id="7a36f-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
