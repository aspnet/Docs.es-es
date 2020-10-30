---
title: SignalR HubContext
author: bradygaster
description: Aprenda a usar el servicio ASP.NET Core SignalR HubContext para enviar notificaciones a los clientes desde fuera de un concentrador.
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058186"
---
# <a name="send-messages-from-outside-a-hub"></a>Envío de mensajes desde fuera de un concentrador

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

El SignalR concentrador es la abstracción principal para enviar mensajes a los clientes conectados SignalR al servidor. También es posible enviar mensajes desde otros lugares de la aplicación mediante el `IHubContext` servicio. En este artículo se explica cómo acceder a SignalR `IHubContext` para enviar notificaciones a los clientes desde fuera de un concentrador.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Obtener una instancia de IHubContext

En ASP.NET Core SignalR , puede tener acceso a una instancia de `IHubContext` a través de la inserción de dependencias. Puede insertar una instancia de `IHubContext` en un controlador, middleware u otro servicio de di. Use la instancia de para enviar mensajes a los clientes.

> [!NOTE]
> Esto difiere de ASP.NET 4. x, SignalR que usaba host global para proporcionar acceso a `IHubContext` . ASP.NET Core tiene un marco de inserción de dependencias que elimina la necesidad de este singleton global.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Inyectar una instancia de IHubContext en un controlador

Puede insertar una instancia de `IHubContext` en un controlador agregándolo a su constructor:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Ahora, con acceso a una instancia de `IHubContext` , puede llamar a los métodos de concentrador como si estuviera en el concentrador.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Obtener una instancia de IHubContext en middleware

Acceda a `IHubContext` dentro de la canalización de middleware de la manera siguiente:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Cuando se llama a los métodos de concentrador desde fuera de la `Hub` clase, no hay ningún llamador asociado a la invocación. Por lo tanto, no hay ningún acceso a las `ConnectionId` `Caller` propiedades, y `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Obtener una instancia de IHubContext desde IHost

El acceso a `IHubContext` desde el host web es útil para la integración con áreas fuera de ASP.net Core, por ejemplo, con marcos de inyección de dependencia de terceros:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Inyectar un HubContext fuertemente tipado

Para insertar un HubContext fuertemente tipado, asegúrese de que el concentrador herede de `Hub<T>` . Insértelo mediante la `IHubContext<THub, T>` interfaz en lugar de `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

Consulte [centros fuertemente tipados](xref:signalr/hubs#strongly-typed-hubs) para obtener más información.

## <a name="related-resources"></a>Recursos relacionados

* [Introducción](xref:tutorials/signalr)
* [Concentradores](xref:signalr/hubs)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
