---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: 'Aprenda a usar el servicio ASP.NET Core :::no-loc(SignalR)::: HubContext para enviar notificaciones a los clientes desde fuera de un concentrador.'
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058186"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="45169-103">Envío de mensajes desde fuera de un concentrador</span><span class="sxs-lookup"><span data-stu-id="45169-103">Send messages from outside a hub</span></span>

<span data-ttu-id="45169-104">Por [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="45169-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="45169-105">El :::no-loc(SignalR)::: concentrador es la abstracción principal para enviar mensajes a los clientes conectados :::no-loc(SignalR)::: al servidor.</span><span class="sxs-lookup"><span data-stu-id="45169-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="45169-106">También es posible enviar mensajes desde otros lugares de la aplicación mediante el `IHubContext` servicio.</span><span class="sxs-lookup"><span data-stu-id="45169-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="45169-107">En este artículo se explica cómo acceder a :::no-loc(SignalR)::: `IHubContext` para enviar notificaciones a los clientes desde fuera de un concentrador.</span><span class="sxs-lookup"><span data-stu-id="45169-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="45169-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="45169-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="45169-109">Obtener una instancia de IHubContext</span><span class="sxs-lookup"><span data-stu-id="45169-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="45169-110">En ASP.NET Core :::no-loc(SignalR)::: , puede tener acceso a una instancia de `IHubContext` a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="45169-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="45169-111">Puede insertar una instancia de `IHubContext` en un controlador, middleware u otro servicio de di.</span><span class="sxs-lookup"><span data-stu-id="45169-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="45169-112">Use la instancia de para enviar mensajes a los clientes.</span><span class="sxs-lookup"><span data-stu-id="45169-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="45169-113">Esto difiere de ASP.NET 4. x, :::no-loc(SignalR)::: que usaba host global para proporcionar acceso a `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="45169-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="45169-114">ASP.NET Core tiene un marco de inserción de dependencias que elimina la necesidad de este singleton global.</span><span class="sxs-lookup"><span data-stu-id="45169-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="45169-115">Inyectar una instancia de IHubContext en un controlador</span><span class="sxs-lookup"><span data-stu-id="45169-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="45169-116">Puede insertar una instancia de `IHubContext` en un controlador agregándolo a su constructor:</span><span class="sxs-lookup"><span data-stu-id="45169-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="45169-117">Ahora, con acceso a una instancia de `IHubContext` , puede llamar a los métodos de concentrador como si estuviera en el concentrador.</span><span class="sxs-lookup"><span data-stu-id="45169-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="45169-118">Obtener una instancia de IHubContext en middleware</span><span class="sxs-lookup"><span data-stu-id="45169-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="45169-119">Acceda a `IHubContext` dentro de la canalización de middleware de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="45169-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="45169-120">Cuando se llama a los métodos de concentrador desde fuera de la `Hub` clase, no hay ningún llamador asociado a la invocación.</span><span class="sxs-lookup"><span data-stu-id="45169-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="45169-121">Por lo tanto, no hay ningún acceso a las `ConnectionId` `Caller` propiedades, y `Others` .</span><span class="sxs-lookup"><span data-stu-id="45169-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="45169-122">Obtener una instancia de IHubContext desde IHost</span><span class="sxs-lookup"><span data-stu-id="45169-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="45169-123">El acceso a `IHubContext` desde el host web es útil para la integración con áreas fuera de ASP.net Core, por ejemplo, con marcos de inyección de dependencia de terceros:</span><span class="sxs-lookup"><span data-stu-id="45169-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="45169-124">Inyectar un HubContext fuertemente tipado</span><span class="sxs-lookup"><span data-stu-id="45169-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="45169-125">Para insertar un HubContext fuertemente tipado, asegúrese de que el concentrador herede de `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="45169-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="45169-126">Insértelo mediante la `IHubContext<THub, T>` interfaz en lugar de `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="45169-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

<span data-ttu-id="45169-127">Consulte [centros fuertemente tipados](xref:signalr/hubs#strongly-typed-hubs) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="45169-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="45169-128">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="45169-128">Related resources</span></span>

* [<span data-ttu-id="45169-129">Introducción</span><span class="sxs-lookup"><span data-stu-id="45169-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="45169-130">Concentradores</span><span class="sxs-lookup"><span data-stu-id="45169-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="45169-131">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="45169-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
