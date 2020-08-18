---
title: Cliente de ASP.NET Core SignalR .net
author: bradygaster
description: Información sobre el cliente de ASP.NET Core SignalR .net
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: e27748e8267a931390f831119a3fd1d45e87745a
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504741"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a><span data-ttu-id="c7f0c-103">Cliente de ASP.NET Core SignalR .net</span><span class="sxs-lookup"><span data-stu-id="c7f0c-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="c7f0c-104">La SignalR biblioteca de cliente de ASP.net Core .net le permite comunicarse con los SignalR concentradores de aplicaciones .net.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="c7f0c-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7f0c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c7f0c-106">El ejemplo de código de este artículo es una aplicación de WPF que usa el SignalR cliente ASP.net Core .net.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-no-locsignalr-net-client-package"></a><span data-ttu-id="c7f0c-107">Instalar el SignalR paquete de cliente de .net</span><span class="sxs-lookup"><span data-stu-id="c7f0c-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="c7f0c-108">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)El paquete de cliente es necesario para que los clientes de .net se conecten a los SignalR concentradores.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c7f0c-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c7f0c-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c7f0c-110">Para instalar la biblioteca de cliente de, ejecute el siguiente comando en la ventana de la **consola del administrador de paquetes** :</span><span class="sxs-lookup"><span data-stu-id="c7f0c-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c7f0c-111">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c7f0c-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c7f0c-112">Para instalar la biblioteca de cliente de, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="c7f0c-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="c7f0c-113">Conexión a un concentrador</span><span class="sxs-lookup"><span data-stu-id="c7f0c-113">Connect to a hub</span></span>

<span data-ttu-id="c7f0c-114">Para establecer una conexión, cree un `HubConnectionBuilder` y llame a `Build` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="c7f0c-115">La dirección URL del concentrador, el protocolo, el tipo de transporte, el nivel de registro, los encabezados y otras opciones se pueden configurar al compilar una conexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="c7f0c-116">Configure las opciones necesarias insertando cualquiera de los `HubConnectionBuilder` métodos en `Build` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="c7f0c-117">Inicie la conexión con `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="c7f0c-118">Controlar la conexión perdida</span><span class="sxs-lookup"><span data-stu-id="c7f0c-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="c7f0c-119">Volver a conectar automáticamente</span><span class="sxs-lookup"><span data-stu-id="c7f0c-119">Automatically reconnect</span></span>

<span data-ttu-id="c7f0c-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Se puede configurar para que se vuelva a conectar automáticamente mediante el `WithAutomaticReconnect` método en <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="c7f0c-121">No se volverá a conectar automáticamente de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="c7f0c-122">Sin parámetros, `WithAutomaticReconnect()` configura el cliente para que espere 0, 2, 10 y 30 segundos, respectivamente, antes de intentar cada intento de reconexión, deteniéndose después de cuatro intentos erróneos.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="c7f0c-123">Antes de iniciar cualquier intento de reconexión, `HubConnection` cambiará al `HubConnectionState.Reconnecting` Estado y desencadenará el `Reconnecting` evento.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="c7f0c-124">Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="c7f0c-125">Las aplicaciones no interactivas pueden iniciar la puesta en cola o la eliminación de mensajes.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="c7f0c-126">Si el cliente se vuelve a conectar correctamente dentro de los cuatro primeros intentos, `HubConnection` volverá a pasar al `Connected` Estado y desencadenará el `Reconnected` evento.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="c7f0c-127">Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha restablecido y quitado de la cola todos los mensajes en cola.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="c7f0c-128">Dado que la conexión es completamente nueva en el servidor, se `ConnectionId` proporcionará un nuevo a los `Reconnected` controladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="c7f0c-129">El `Reconnected` parámetro del controlador de eventos será `connectionId` null si `HubConnection` se configuró para [omitir la negociación](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="c7f0c-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="c7f0c-130">`WithAutomaticReconnect()` no configurará el `HubConnection` para reintentar errores de inicio inicial, por lo que los errores de inicio deben controlarse manualmente:</span><span class="sxs-lookup"><span data-stu-id="c7f0c-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="c7f0c-131">Si el cliente no se vuelve a conectar correctamente en los cuatro primeros intentos, `HubConnection` cambiará al `Disconnected` Estado y desencadenará el <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="c7f0c-132">Esto proporciona una oportunidad para intentar reiniciar la conexión manualmente o informar a los usuarios de que la conexión se ha perdido permanentemente.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="c7f0c-133">Con el fin de configurar un número personalizado de intentos de reconexión antes de desconectar o cambiar el tiempo de reconexión, `WithAutomaticReconnect` acepta una matriz de números que representan el retraso en milisegundos que se debe esperar antes de iniciar cada intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="c7f0c-134">En el ejemplo anterior `HubConnection` se configura para iniciar el intento de reconexión inmediatamente después de la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="c7f0c-135">Esto también se aplica a la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="c7f0c-136">Si se produce un error en el primer intento de reconexión, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c7f0c-137">Si se produce un error en el segundo intento de reconexión, el tercer intento de reconexión se iniciará en 10 segundos, lo que volverá a ser como la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="c7f0c-138">Después, el comportamiento personalizado difiere de nuevo del comportamiento predeterminado al detenerse después del tercer error de intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="c7f0c-139">En la configuración predeterminada, se producirá un intento de reconexión más en otros 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="c7f0c-140">Si desea tener un mayor control sobre la temporización y el número de intentos de reconexión automática, `WithAutomaticReconnect` acepta un objeto `IRetryPolicy` que implementa la interfaz, que tiene un único método denominado `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="c7f0c-141">`NextRetryDelay` toma un único argumento con el tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="c7f0c-142">`RetryContext`Tiene tres propiedades: `PreviousRetryCount` , `ElapsedTime` y `RetryReason` , que son `long` , y, `TimeSpan` `Exception` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="c7f0c-143">Antes del primer intento de reconexión, `PreviousRetryCount` y serán cero y será `ElapsedTime` `RetryReason` la excepción que provocó la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="c7f0c-144">Después de cada intento de reintento erróneo, se `PreviousRetryCount` incrementará en uno, se `ElapsedTime` actualizará para reflejar la cantidad de tiempo empleado en la reconexión hasta el momento, y será `RetryReason` la excepción que provocó el último error en el intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="c7f0c-145">`NextRetryDelay` debe devolver un valor TimeSpan que representa el tiempo de espera antes del siguiente intento de reconexión o `null` si `HubConnection` debe dejar de volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="c7f0c-146">Como alternativa, puede escribir código que volverá a conectar el cliente manualmente, tal como se muestra en [reconexión manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="c7f0c-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="c7f0c-147">Volver a conectar manualmente</span><span class="sxs-lookup"><span data-stu-id="c7f0c-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="c7f0c-148">Antes de 3,0, el cliente de .NET para SignalR no se vuelve a conectar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="c7f0c-149">Debe escribir código que volverá a conectar el cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="c7f0c-150">Utilice el <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento para responder a una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="c7f0c-151">Por ejemplo, puede que desee automatizar la reconexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="c7f0c-152">El `Closed` evento requiere un delegado que devuelve `Task` , que permite que el código asincrónico se ejecute sin usar `async void` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="c7f0c-153">Para satisfacer la firma del delegado en un `Closed` controlador de eventos que se ejecuta sincrónicamente, devuelva `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="c7f0c-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="c7f0c-154">La razón principal de la compatibilidad con Async es para que pueda reiniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="c7f0c-155">Iniciar una conexión es una acción asincrónica.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="c7f0c-156">En un `Closed` controlador que reinicie la conexión, considere la posibilidad de esperar algún retraso aleatorio para evitar la sobrecarga del servidor, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c7f0c-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="c7f0c-157">Llamar a métodos de Hub desde el cliente</span><span class="sxs-lookup"><span data-stu-id="c7f0c-157">Call hub methods from client</span></span>

<span data-ttu-id="c7f0c-158">`InvokeAsync` llama a los métodos en el concentrador.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="c7f0c-159">Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="c7f0c-160">SignalR es asincrónica, por lo que debe usar `async` y `await` al realizar las llamadas.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="c7f0c-161">El `InvokeAsync` método devuelve un `Task` que se completa cuando el método de servidor devuelve.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="c7f0c-162">El valor devuelto, si existe, se proporciona como resultado de `Task` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="c7f0c-163">Cualquier excepción producida por el método en el servidor produce un error `Task` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="c7f0c-164">Use `await` la sintaxis para esperar a que se complete el método de servidor y la `try...catch` sintaxis para controlar los errores.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="c7f0c-165">El `SendAsync` método devuelve un `Task` que se completa cuando se envía el mensaje al servidor.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="c7f0c-166">No se proporciona ningún valor devuelto, ya que esto `Task` no espera hasta que se complete el método del servidor.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="c7f0c-167">Las excepciones producidas en el cliente mientras se envía el mensaje producen un error `Task` .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="c7f0c-168">Use `await` la `try...catch` sintaxis y para controlar los errores de envío.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="c7f0c-169">La llamada a métodos de concentrador desde un cliente solo se admite cuando se usa el SignalR servicio de Azure en el modo *predeterminado* .</span><span class="sxs-lookup"><span data-stu-id="c7f0c-169">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="c7f0c-170">Para obtener más información, consulte preguntas más frecuentes [(repositorio de github de Azure signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="c7f0c-170">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="c7f0c-171">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="c7f0c-171">Call client methods from hub</span></span>

<span data-ttu-id="c7f0c-172">Defina los métodos que el concentrador llama mediante `connection.On` después de compilar, pero antes de iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="c7f0c-173">El código anterior en `connection.On` se ejecuta cuando el código del lado servidor lo llama mediante el `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="c7f0c-174">Registro y control de errores</span><span class="sxs-lookup"><span data-stu-id="c7f0c-174">Error handling and logging</span></span>

<span data-ttu-id="c7f0c-175">Controlar los errores con una instrucción try-catch.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="c7f0c-176">Inspeccione el `Exception` objeto para determinar la acción adecuada que se realizará después de que se produzca un error.</span><span class="sxs-lookup"><span data-stu-id="c7f0c-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="c7f0c-177">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c7f0c-177">Additional resources</span></span>

* [<span data-ttu-id="c7f0c-178">Concentradores</span><span class="sxs-lookup"><span data-stu-id="c7f0c-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c7f0c-179">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c7f0c-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c7f0c-180">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="c7f0c-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="c7f0c-181">Documentación sin servidor del servicio de Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="c7f0c-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
