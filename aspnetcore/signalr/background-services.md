---
title: ASP.NET Core host SignalR en los servicios en segundo plano
author: bradygaster
description: Obtenga información sobre cómo enviar mensajes a SignalR los clientes desde clases BackgroundService de .net Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 9d95d7c9a33bcf2f4a603d815269752124133ee6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589704"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core host SignalR en los servicios en segundo plano

Por el [Brady](https://twitter.com/bradygaster)

En este artículo se proporcionan instrucciones para:

* Hospedar los SignalR concentradores mediante un proceso de trabajo en segundo plano hospedado con ASP.net Core.
* Envío de mensajes a clientes conectados desde un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)de .net Core.

::: moniker range=">= aspnetcore-3.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/3.x) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/2.2) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>Habilitar SignalR en el inicio

::: moniker range=">= aspnetcore-3.0"

Hospedar ASP.NET Core SignalR hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación web ASP.net Core. En el `Startup.ConfigureServices` método, la llamada a `services.AddSignalR` agrega los servicios necesarios a la capa de ASP.net Core de la inserción de dependencias (di) para admitir SignalR . En `Startup.Configure` , `MapHub` se llama al método en la `UseEndpoints` devolución de llamada para conectar los puntos de conexión del concentrador en el ASP.net Core canalización de solicitudes.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

Hospedar ASP.NET Core SignalR hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación web ASP.net Core. En el `Startup.ConfigureServices` método, la llamada a `services.AddSignalR` agrega los servicios necesarios a la capa de ASP.net Core de la inserción de dependencias (di) para admitir SignalR . En `Startup.Configure` , `UseSignalR` se llama al método para conectar los puntos de conexión del concentrador en la canalización de solicitud de ASP.net Core.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

En el ejemplo anterior, la `ClockHub` clase implementa la `Hub<T>` clase para crear un concentrador fuertemente tipado. Se ha `ClockHub` configurado en la `Startup` clase para responder a las solicitudes en el extremo `/hubs/clock` .

Para obtener más información sobre los concentradores fuertemente tipados, consulte [uso de hubs en SignalR para ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Esta funcionalidad no se limita a la clase [Hub \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Cualquier clase que herede de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funciona.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

La interfaz utilizada por el fuertemente tipado `ClockHub` es la `IClock` interfaz.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>Llamar a un SignalR centro desde un servicio en segundo plano

Durante el inicio, la `Worker` clase, a `BackgroundService` , se habilita mediante `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Puesto que SignalR también se habilita durante la `Startup` fase, en la que cada concentrador se adjunta a un punto de conexión individual en la canalización de solicitudes HTTP de ASP.net Core, cada concentrador se representa mediante un `IHubContext<T>` en el servidor. Mediante el uso de las características DI de ASP.NET Core, otras clases a las que se ha creado una instancia de la capa de hospedaje, como `BackgroundService` las clases, las clases de controlador de MVC o los Razor modelos de página, pueden obtener referencias a los concentradores del lado servidor aceptando instancias de `IHubContext<ClockHub, IClock>` durante la construcción.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

A medida que `ExecuteAsync` se llama al método de forma iterativa en el servicio en segundo plano, la fecha y hora actuales del servidor se envían a los clientes conectados mediante `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>Reaccionar a SignalR eventos con servicios en segundo plano

Al igual que una aplicación de una sola página que usa el cliente de JavaScript para SignalR o una aplicación de escritorio de .net <xref:signalr/dotnet-client> que usa, `BackgroundService` `IHostedService` también se puede usar una implementación de o para conectarse a SignalR los concentradores y responder a los eventos.

La `ClockHubClient` clase implementa la `IClock` interfaz y la `IHostedService` interfaz. De este modo, se puede habilitar durante `Startup` la ejecución continua y responder a los eventos del concentrador desde el servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante la inicialización, `ClockHubClient` crea una instancia de `HubConnection` y habilita el `IClock.ShowTime` método como controlador para el evento del centro `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción](xref:tutorials/signalr)
* [Concentradores](xref:signalr/hubs)
* [Publicar en Azure](xref:signalr/publish-to-azure-web-app)
* [Concentradores fuertemente tipados](xref:signalr/hubs#strongly-typed-hubs)
