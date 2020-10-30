---
title: 'ASP.NET Core host :::no-loc(SignalR)::: en los servicios en segundo plano'
author: bradygaster
description: 'Obtenga información sobre cómo enviar mensajes a :::no-loc(SignalR)::: los clientes desde clases BackgroundService de .net Core.'
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 810eff7ccb08ecc22ea255bf0a9fe3d22637179f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060110"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a><span data-ttu-id="9ce8e-103">ASP.NET Core host :::no-loc(SignalR)::: en los servicios en segundo plano</span><span class="sxs-lookup"><span data-stu-id="9ce8e-103">Host ASP.NET Core :::no-loc(SignalR)::: in background services</span></span>

<span data-ttu-id="9ce8e-104">Por el [Brady](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="9ce8e-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="9ce8e-105">En este artículo se proporcionan instrucciones para:</span><span class="sxs-lookup"><span data-stu-id="9ce8e-105">This article provides guidance for:</span></span>

* <span data-ttu-id="9ce8e-106">Hospedar los :::no-loc(SignalR)::: concentradores mediante un proceso de trabajo en segundo plano hospedado con ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-106">Hosting :::no-loc(SignalR)::: Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="9ce8e-107">Envío de mensajes a clientes conectados desde un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)de .net Core.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ce8e-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9ce8e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9ce8e-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9ce8e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a><span data-ttu-id="9ce8e-110">Habilitar :::no-loc(SignalR)::: en el inicio</span><span class="sxs-lookup"><span data-stu-id="9ce8e-110">Enable :::no-loc(SignalR)::: in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ce8e-111">Hospedar ASP.NET Core :::no-loc(SignalR)::: hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación web ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-111">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="9ce8e-112">En el `Startup.ConfigureServices` método, la llamada a `services.Add:::no-loc(SignalR):::` agrega los servicios necesarios a la capa de ASP.net Core de la inserción de dependencias (di) para admitir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-112">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="9ce8e-113">En `Startup.Configure` , `MapHub` se llama al método en la `UseEndpoints` devolución de llamada para conectar los puntos de conexión del concentrador en el ASP.net Core canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9ce8e-114">Hospedar ASP.NET Core :::no-loc(SignalR)::: hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación web ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-114">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="9ce8e-115">En el `Startup.ConfigureServices` método, la llamada a `services.Add:::no-loc(SignalR):::` agrega los servicios necesarios a la capa de ASP.net Core de la inserción de dependencias (di) para admitir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-115">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="9ce8e-116">En `Startup.Configure` , `Use:::no-loc(SignalR):::` se llama al método para conectar los puntos de conexión del concentrador en la canalización de solicitud de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-116">In `Startup.Configure`, the `Use:::no-loc(SignalR):::` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="9ce8e-117">En el ejemplo anterior, la `ClockHub` clase implementa la `Hub<T>` clase para crear un concentrador fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="9ce8e-118">Se ha `ClockHub` configurado en la `Startup` clase para responder a las solicitudes en el extremo `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="9ce8e-119">Para obtener más información sobre los concentradores fuertemente tipados, consulte [uso de hubs en :::no-loc(SignalR)::: para ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="9ce8e-119">For more information on strongly typed Hubs, see [Use hubs in :::no-loc(SignalR)::: for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="9ce8e-120">Esta funcionalidad no se limita a la clase [Hub \<T> ](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1) class.</span></span> <span data-ttu-id="9ce8e-121">Cualquier clase que herede de [Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub), funciona.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="9ce8e-122">La interfaz utilizada por el fuertemente tipado `ClockHub` es la `IClock` interfaz.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="9ce8e-123">Llamar a un :::no-loc(SignalR)::: centro desde un servicio en segundo plano</span><span class="sxs-lookup"><span data-stu-id="9ce8e-123">Call a :::no-loc(SignalR)::: Hub from a background service</span></span>

<span data-ttu-id="9ce8e-124">Durante el inicio, la `Worker` clase, a `BackgroundService` , se habilita mediante `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="9ce8e-125">Puesto que :::no-loc(SignalR)::: también se habilita durante la `Startup` fase, en la que cada concentrador se adjunta a un punto de conexión individual en la canalización de solicitudes HTTP de ASP.net Core, cada concentrador se representa mediante un `IHubContext<T>` en el servidor.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-125">Since :::no-loc(SignalR)::: is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="9ce8e-126">Mediante el uso de las características DI de ASP.NET Core, otras clases a las que se ha creado una instancia de la capa de hospedaje, como `BackgroundService` las clases, las clases de controlador de MVC o los :::no-loc(Razor)::: modelos de página, pueden obtener referencias a los concentradores del lado servidor aceptando instancias de `IHubContext<ClockHub, IClock>` durante la construcción.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or :::no-loc(Razor)::: page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="9ce8e-127">A medida que `ExecuteAsync` se llama al método de forma iterativa en el servicio en segundo plano, la fecha y hora actuales del servidor se envían a los clientes conectados mediante `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-no-locsignalr-events-with-background-services"></a><span data-ttu-id="9ce8e-128">Reaccionar a :::no-loc(SignalR)::: eventos con servicios en segundo plano</span><span class="sxs-lookup"><span data-stu-id="9ce8e-128">React to :::no-loc(SignalR)::: events with background services</span></span>

<span data-ttu-id="9ce8e-129">Al igual que una aplicación de una sola página que usa el cliente de JavaScript para :::no-loc(SignalR)::: o una aplicación de escritorio de .net puede usar mediante el <xref:signalr/dotnet-client> , `BackgroundService` `IHostedService` también se puede usar una implementación de o para conectarse a :::no-loc(SignalR)::: los concentradores y responder a eventos.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-129">Like a Single Page App using the JavaScript client for :::no-loc(SignalR)::: or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to :::no-loc(SignalR)::: Hubs and respond to events.</span></span>

<span data-ttu-id="9ce8e-130">La `ClockHubClient` clase implementa la `IClock` interfaz y la `IHostedService` interfaz.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="9ce8e-131">De este modo, se puede habilitar durante `Startup` la ejecución continua y responder a los eventos del concentrador desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="9ce8e-132">Durante la inicialización, `ClockHubClient` crea una instancia de `HubConnection` y habilita el `IClock.ShowTime` método como controlador para el evento del centro `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="9ce8e-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="9ce8e-133">En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="9ce8e-134">Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="9ce8e-135">En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="9ce8e-136">Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="9ce8e-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9ce8e-137">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9ce8e-137">Additional resources</span></span>

* [<span data-ttu-id="9ce8e-138">Introducción</span><span class="sxs-lookup"><span data-stu-id="9ce8e-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9ce8e-139">Concentradores</span><span class="sxs-lookup"><span data-stu-id="9ce8e-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9ce8e-140">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="9ce8e-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="9ce8e-141">Concentradores fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="9ce8e-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
