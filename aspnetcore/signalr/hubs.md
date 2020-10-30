---
title: 'Usar hubs en ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: 'Obtenga información sobre cómo usar hubs en ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 4a31c16eb44e2244574d0df49c30e7a44b2bba6e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050945"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="3ce15-103">Usar hubs en :::no-loc(SignalR)::: para ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="3ce15-103">Use hubs in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

<span data-ttu-id="3ce15-104">Por [Rachel Appel](https://twitter.com/rachelappel) y [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="3ce15-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="3ce15-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3ce15-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-no-locsignalr-hub"></a><span data-ttu-id="3ce15-106">¿Qué es un :::no-loc(SignalR)::: centro?</span><span class="sxs-lookup"><span data-stu-id="3ce15-106">What is a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="3ce15-107">La :::no-loc(SignalR)::: API de hubs le permite llamar a métodos en clientes conectados desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="3ce15-107">The :::no-loc(SignalR)::: Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="3ce15-108">En el código del servidor, se definen los métodos a los que llama el cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="3ce15-109">En el código de cliente, se definen los métodos a los que se llama desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="3ce15-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="3ce15-110">:::no-loc(SignalR)::: se encarga de todo en segundo plano que permite la comunicación de cliente a servidor y de servidor a cliente en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="3ce15-110">:::no-loc(SignalR)::: takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-no-locsignalr-hubs"></a><span data-ttu-id="3ce15-111">Configuración de :::no-loc(SignalR)::: centros</span><span class="sxs-lookup"><span data-stu-id="3ce15-111">Configure :::no-loc(SignalR)::: hubs</span></span>

<span data-ttu-id="3ce15-112">El :::no-loc(SignalR)::: middleware requiere algunos servicios, que se configuran mediante una llamada a `services.Add:::no-loc(SignalR):::` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-112">The :::no-loc(SignalR)::: middleware requires some services, which are configured by calling `services.Add:::no-loc(SignalR):::`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3ce15-113">Al agregar :::no-loc(SignalR)::: funcionalidad a una aplicación ASP.net Core, el programa :::no-loc(SignalR)::: de instalación enruta `endpoint.MapHub` la llamada en la `Startup.Configure` devolución de llamada del método `app.UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-113">When adding :::no-loc(SignalR)::: functionality to an ASP.NET Core app, setup :::no-loc(SignalR)::: routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3ce15-114">Al agregar :::no-loc(SignalR)::: funcionalidad a una aplicación ASP.net Core, el programa :::no-loc(SignalR)::: de instalación de enruta mediante una llamada a `app.Use:::no-loc(SignalR):::` en el `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-114">When adding :::no-loc(SignalR)::: functionality to an ASP.NET Core app, setup :::no-loc(SignalR)::: routes by calling `app.Use:::no-loc(SignalR):::` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="3ce15-115">Crear y usar hubs</span><span class="sxs-lookup"><span data-stu-id="3ce15-115">Create and use hubs</span></span>

<span data-ttu-id="3ce15-116">Cree un centro declarando una clase que herede de `Hub` y agréguele métodos públicos.</span><span class="sxs-lookup"><span data-stu-id="3ce15-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="3ce15-117">Los clientes pueden llamar a métodos definidos como `public` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="3ce15-118">Puede especificar un tipo de valor devuelto y parámetros, incluidos tipos complejos y matrices, como haría en cualquier método de C#.</span><span class="sxs-lookup"><span data-stu-id="3ce15-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="3ce15-119">:::no-loc(SignalR)::: controla la serialización y deserialización de objetos y matrices complejos en los parámetros y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="3ce15-119">:::no-loc(SignalR)::: handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="3ce15-120">Los concentradores son transitorios:</span><span class="sxs-lookup"><span data-stu-id="3ce15-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="3ce15-121">No almacene el estado en una propiedad de la clase hub.</span><span class="sxs-lookup"><span data-stu-id="3ce15-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="3ce15-122">Cada llamada al método de concentrador se ejecuta en una nueva instancia del concentrador.</span><span class="sxs-lookup"><span data-stu-id="3ce15-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="3ce15-123">Se usa `await` al llamar a métodos asincrónicos que dependen de que el concentrador permanezca activo.</span><span class="sxs-lookup"><span data-stu-id="3ce15-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="3ce15-124">Por ejemplo, un método como `Clients.All.SendAsync(...)` puede producir un error si se llama sin `await` y el método de concentrador se completa antes de que `SendAsync` finalice.</span><span class="sxs-lookup"><span data-stu-id="3ce15-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="3ce15-125">El objeto de contexto</span><span class="sxs-lookup"><span data-stu-id="3ce15-125">The Context object</span></span>

<span data-ttu-id="3ce15-126">La `Hub` clase tiene una `Context` propiedad que contiene las siguientes propiedades con información sobre la conexión:</span><span class="sxs-lookup"><span data-stu-id="3ce15-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="3ce15-127">Propiedad</span><span class="sxs-lookup"><span data-stu-id="3ce15-127">Property</span></span> | <span data-ttu-id="3ce15-128">Descripción</span><span class="sxs-lookup"><span data-stu-id="3ce15-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="3ce15-129">Obtiene el identificador único para la conexión, asignado por :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="3ce15-129">Gets the unique ID for the connection, assigned by :::no-loc(SignalR):::.</span></span> <span data-ttu-id="3ce15-130">Hay un identificador de conexión para cada conexión.</span><span class="sxs-lookup"><span data-stu-id="3ce15-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="3ce15-131">Obtiene el [identificador de usuario](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="3ce15-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="3ce15-132">De forma predeterminada, :::no-loc(SignalR)::: usa el objeto `ClaimTypes.NameIdentifier` de la clase `ClaimsPrincipal` asociada a la conexión como identificador de usuario.</span><span class="sxs-lookup"><span data-stu-id="3ce15-132">By default, :::no-loc(SignalR)::: uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="3ce15-133">Obtiene el objeto `ClaimsPrincipal` asociado al usuario actual.</span><span class="sxs-lookup"><span data-stu-id="3ce15-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="3ce15-134">Obtiene una colección de clave/valor que se puede utilizar para compartir datos dentro del ámbito de esta conexión.</span><span class="sxs-lookup"><span data-stu-id="3ce15-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="3ce15-135">Los datos se pueden almacenar en esta colección y se conservarán para la conexión entre las distintas invocaciones de métodos de concentrador.</span><span class="sxs-lookup"><span data-stu-id="3ce15-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="3ce15-136">Obtiene la colección de características disponibles en la conexión.</span><span class="sxs-lookup"><span data-stu-id="3ce15-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="3ce15-137">Por ahora, esta colección no es necesaria en la mayoría de los escenarios, por lo que aún no está documentada en detalle.</span><span class="sxs-lookup"><span data-stu-id="3ce15-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="3ce15-138">Obtiene un `CancellationToken` que notifica cuando se anula la conexión.</span><span class="sxs-lookup"><span data-stu-id="3ce15-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="3ce15-139">`Hub.Context` también contiene los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3ce15-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="3ce15-140">Método</span><span class="sxs-lookup"><span data-stu-id="3ce15-140">Method</span></span> | <span data-ttu-id="3ce15-141">Descripción</span><span class="sxs-lookup"><span data-stu-id="3ce15-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="3ce15-142">Devuelve el objeto `HttpContext` para la conexión o `null` si la conexión no está asociada a una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="3ce15-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="3ce15-143">En el caso de las conexiones HTTP, puede usar este método para obtener información como encabezados HTTP y cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="3ce15-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="3ce15-144">Anula la conexión.</span><span class="sxs-lookup"><span data-stu-id="3ce15-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="3ce15-145">Objeto clients</span><span class="sxs-lookup"><span data-stu-id="3ce15-145">The Clients object</span></span>

<span data-ttu-id="3ce15-146">La `Hub` clase tiene una `Clients` propiedad que contiene las siguientes propiedades para la comunicación entre el servidor y el cliente:</span><span class="sxs-lookup"><span data-stu-id="3ce15-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="3ce15-147">Propiedad</span><span class="sxs-lookup"><span data-stu-id="3ce15-147">Property</span></span> | <span data-ttu-id="3ce15-148">Descripción</span><span class="sxs-lookup"><span data-stu-id="3ce15-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="3ce15-149">Llama a un método en todos los clientes conectados</span><span class="sxs-lookup"><span data-stu-id="3ce15-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="3ce15-150">Llama a un método en el cliente que invocó el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="3ce15-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="3ce15-151">Llama a un método en todos los clientes conectados, excepto el cliente que invocó el método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="3ce15-152">`Hub.Clients` también contiene los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3ce15-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="3ce15-153">Método</span><span class="sxs-lookup"><span data-stu-id="3ce15-153">Method</span></span> | <span data-ttu-id="3ce15-154">Descripción</span><span class="sxs-lookup"><span data-stu-id="3ce15-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="3ce15-155">Llama a un método en todos los clientes conectados, excepto las conexiones especificadas.</span><span class="sxs-lookup"><span data-stu-id="3ce15-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="3ce15-156">Llama a un método en un cliente conectado específico</span><span class="sxs-lookup"><span data-stu-id="3ce15-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="3ce15-157">Llama a un método en clientes conectados específicos</span><span class="sxs-lookup"><span data-stu-id="3ce15-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="3ce15-158">Llama a un método en todas las conexiones del grupo especificado</span><span class="sxs-lookup"><span data-stu-id="3ce15-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="3ce15-159">Llama a un método en todas las conexiones del grupo especificado, excepto las conexiones especificadas.</span><span class="sxs-lookup"><span data-stu-id="3ce15-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="3ce15-160">Llama a un método en varios grupos de conexiones</span><span class="sxs-lookup"><span data-stu-id="3ce15-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="3ce15-161">Llama a un método en un grupo de conexiones, excluido el cliente que invocó el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="3ce15-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="3ce15-162">Llama a un método en todas las conexiones asociadas a un usuario específico</span><span class="sxs-lookup"><span data-stu-id="3ce15-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="3ce15-163">Llama a un método en todas las conexiones asociadas a los usuarios especificados</span><span class="sxs-lookup"><span data-stu-id="3ce15-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="3ce15-164">Cada propiedad o método de las tablas anteriores devuelve un objeto con un `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="3ce15-165">El `SendAsync` método permite proporcionar el nombre y los parámetros del método de cliente que se va a llamar.</span><span class="sxs-lookup"><span data-stu-id="3ce15-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="3ce15-166">Enviar mensajes a los clientes</span><span class="sxs-lookup"><span data-stu-id="3ce15-166">Send messages to clients</span></span>

<span data-ttu-id="3ce15-167">Para realizar llamadas a clientes específicos, utilice las propiedades del `Clients` objeto.</span><span class="sxs-lookup"><span data-stu-id="3ce15-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="3ce15-168">En el ejemplo siguiente, hay tres métodos de concentrador:</span><span class="sxs-lookup"><span data-stu-id="3ce15-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="3ce15-169">`SendMessage` envía un mensaje a todos los clientes conectados mediante `Clients.All` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="3ce15-170">`SendMessageToCaller` Devuelve un mensaje al autor de la llamada mediante `Clients.Caller` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="3ce15-171">`SendMessageToGroups` envía un mensaje a todos los clientes del `:::no-loc(SignalR)::: Users` grupo.</span><span class="sxs-lookup"><span data-stu-id="3ce15-171">`SendMessageToGroups` sends a message to all clients in the `:::no-loc(SignalR)::: Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="3ce15-172">Concentradores fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="3ce15-172">Strongly typed hubs</span></span>

<span data-ttu-id="3ce15-173">Un inconveniente de usar `SendAsync` es que se basa en una cadena mágica para especificar el método de cliente al que se va a llamar.</span><span class="sxs-lookup"><span data-stu-id="3ce15-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="3ce15-174">Esto deja el código abierto en los errores en tiempo de ejecución si el nombre del método está mal escrito o falta en el cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="3ce15-175">Una alternativa al uso de `SendAsync` es para escribir fuertemente `Hub` con <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub%601> .</span><span class="sxs-lookup"><span data-stu-id="3ce15-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub%601>.</span></span> <span data-ttu-id="3ce15-176">En el ejemplo siguiente, los `ChatHub` métodos de cliente se han extraído en una interfaz denominada `IChatClient` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="3ce15-177">Esta interfaz se puede usar para refactorizar el `ChatHub` ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="3ce15-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="3ce15-178">`Hub<IChatClient>`El uso de habilita la comprobación en tiempo de compilación de los métodos de cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="3ce15-179">Esto evita problemas causados por el uso de cadenas mágicas, ya que `Hub<T>` solo puede proporcionar acceso a los métodos definidos en la interfaz.</span><span class="sxs-lookup"><span data-stu-id="3ce15-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="3ce15-180">El uso de un fuertemente tipado `Hub<T>` deshabilita la capacidad de usar `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="3ce15-181">Cualquier método definido en la interfaz todavía se puede definir como asincrónico.</span><span class="sxs-lookup"><span data-stu-id="3ce15-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="3ce15-182">De hecho, cada uno de estos métodos debe devolver `Task` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="3ce15-183">Dado que es una interfaz, no use la `async` palabra clave.</span><span class="sxs-lookup"><span data-stu-id="3ce15-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="3ce15-184">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3ce15-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="3ce15-185">El `Async` sufijo no se elimina del nombre del método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="3ce15-186">A menos que el método de cliente se defina con `.on('MyMethodAsync')` , no se debe usar `MyMethodAsync` como nombre.</span><span class="sxs-lookup"><span data-stu-id="3ce15-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="3ce15-187">Cambiar el nombre de un método de concentrador</span><span class="sxs-lookup"><span data-stu-id="3ce15-187">Change the name of a hub method</span></span>

<span data-ttu-id="3ce15-188">De forma predeterminada, un nombre de método de concentrador de servidor es el nombre del método de .NET.</span><span class="sxs-lookup"><span data-stu-id="3ce15-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="3ce15-189">Sin embargo, puede usar el atributo [HubMethodName](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubMethodNameAttribute) para cambiar este valor predeterminado y especificar manualmente un nombre para el método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="3ce15-190">El cliente debe utilizar este nombre, en lugar del nombre del método .NET, al invocar el método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="3ce15-191">Controlar eventos para una conexión</span><span class="sxs-lookup"><span data-stu-id="3ce15-191">Handle events for a connection</span></span>

<span data-ttu-id="3ce15-192">La :::no-loc(SignalR)::: API de hubs proporciona `OnConnectedAsync` los `OnDisconnectedAsync` métodos virtuales y para administrar y realizar un seguimiento de las conexiones.</span><span class="sxs-lookup"><span data-stu-id="3ce15-192">The :::no-loc(SignalR)::: Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="3ce15-193">Invalide el `OnConnectedAsync` método virtual para realizar acciones cuando un cliente se conecta al centro, como agregarlo a un grupo.</span><span class="sxs-lookup"><span data-stu-id="3ce15-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="3ce15-194">Invalide el `OnDisconnectedAsync` método virtual para realizar acciones cuando un cliente se desconecte.</span><span class="sxs-lookup"><span data-stu-id="3ce15-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="3ce15-195">Si el cliente se desconecta intencionadamente (mediante una llamada a `connection.stop()` , por ejemplo), el `exception` parámetro será `null` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="3ce15-196">Sin embargo, si el cliente está desconectado debido a un error (por ejemplo, un error de red), el parámetro contendrá `exception` una excepción que describe el error.</span><span class="sxs-lookup"><span data-stu-id="3ce15-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="3ce15-197">Control de errores</span><span class="sxs-lookup"><span data-stu-id="3ce15-197">Handle errors</span></span>

<span data-ttu-id="3ce15-198">Las excepciones iniciadas en los métodos de concentrador se envían al cliente que invocó el método.</span><span class="sxs-lookup"><span data-stu-id="3ce15-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="3ce15-199">En el cliente de JavaScript, el `invoke` método devuelve un [compromiso de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="3ce15-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="3ce15-200">Cuando el cliente recibe un error con un controlador asociado a la promesa mediante `catch` , se invoca y se pasa como un objeto de JavaScript `Error` .</span><span class="sxs-lookup"><span data-stu-id="3ce15-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="3ce15-201">Si el centro inicia una excepción, las conexiones no se cierran.</span><span class="sxs-lookup"><span data-stu-id="3ce15-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="3ce15-202">De forma predeterminada, :::no-loc(SignalR)::: devuelve un mensaje de error genérico al cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-202">By default, :::no-loc(SignalR)::: returns a generic error message to the client.</span></span> <span data-ttu-id="3ce15-203">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3ce15-203">For example:</span></span>

```
Microsoft.AspNetCore.:::no-loc(SignalR):::.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="3ce15-204">Las excepciones inesperadas a menudo contienen información confidencial, como el nombre de un servidor de base de datos en una excepción que se desencadena cuando se produce un error en la conexión de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3ce15-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="3ce15-205">:::no-loc(SignalR)::: no expone estos mensajes de error detallados de forma predeterminada como medida de seguridad.</span><span class="sxs-lookup"><span data-stu-id="3ce15-205">:::no-loc(SignalR)::: doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="3ce15-206">Vea el [artículo sobre consideraciones de seguridad](xref:signalr/security#exceptions) para obtener más información sobre por qué se suprimen los detalles de la excepción.</span><span class="sxs-lookup"><span data-stu-id="3ce15-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="3ce15-207">Si tiene una condición *excepcional que desea* propagar al cliente, puede utilizar la `HubException` clase.</span><span class="sxs-lookup"><span data-stu-id="3ce15-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="3ce15-208">Si inicia una excepción `HubException` desde el método de concentrador, :::no-loc(SignalR)::: **will** enviará todo el mensaje al cliente, sin modificar.</span><span class="sxs-lookup"><span data-stu-id="3ce15-208">If you throw a `HubException` from your hub method, :::no-loc(SignalR)::: **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="3ce15-209">:::no-loc(SignalR)::: solo envía la `Message` propiedad de la excepción al cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-209">:::no-loc(SignalR)::: only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="3ce15-210">El seguimiento de la pila y otras propiedades de la excepción no están disponibles para el cliente.</span><span class="sxs-lookup"><span data-stu-id="3ce15-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3ce15-211">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="3ce15-211">Related resources</span></span>

* [<span data-ttu-id="3ce15-212">Introducción a ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="3ce15-212">Intro to ASP.NET Core :::no-loc(SignalR):::</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="3ce15-213">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="3ce15-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="3ce15-214">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="3ce15-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
