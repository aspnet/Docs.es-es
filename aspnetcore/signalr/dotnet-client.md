---
title: Cliente de ASP.NET Core SignalR .net
author: bradygaster
description: Información sobre el cliente de ASP.NET Core SignalR .net
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: f56beb634689b42aec3fe193c389e52de746c92a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634012"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a>Cliente de ASP.NET Core SignalR .net

La SignalR biblioteca de cliente de ASP.net Core .net le permite comunicarse con los SignalR concentradores de aplicaciones .net.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

El ejemplo de código de este artículo es una aplicación de WPF que usa el SignalR cliente ASP.net Core .net.

## <a name="install-the-no-locsignalr-net-client-package"></a>Instalar el SignalR paquete de cliente de .net

[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)El paquete de cliente es necesario para que los clientes de .net se conecten a los SignalR concentradores.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para instalar la biblioteca de cliente de, ejecute el siguiente comando en la ventana de la **consola del administrador de paquetes** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Para instalar la biblioteca de cliente de, ejecute el siguiente comando en un shell de comandos:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Conexión a un concentrador

Para establecer una conexión, cree un `HubConnectionBuilder` y llame a `Build` . La dirección URL del concentrador, el protocolo, el tipo de transporte, el nivel de registro, los encabezados y otras opciones se pueden configurar al compilar una conexión. Configure las opciones necesarias insertando cualquiera de los `HubConnectionBuilder` métodos en `Build` . Inicie la conexión con `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Controlar la conexión perdida

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Volver a conectar automáticamente

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Se puede configurar para que se vuelva a conectar automáticamente mediante el `WithAutomaticReconnect` método en <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . No se volverá a conectar automáticamente de forma predeterminada.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Sin parámetros, `WithAutomaticReconnect()` configura el cliente para que espere 0, 2, 10 y 30 segundos, respectivamente, antes de intentar cada intento de reconexión, deteniéndose después de cuatro intentos erróneos.

Antes de iniciar cualquier intento de reconexión, `HubConnection` cambiará al `HubConnectionState.Reconnecting` Estado y desencadenará el `Reconnecting` evento.  Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario. Las aplicaciones no interactivas pueden iniciar la puesta en cola o la eliminación de mensajes.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Si el cliente se vuelve a conectar correctamente dentro de los cuatro primeros intentos, `HubConnection` volverá a pasar al `Connected` Estado y desencadenará el `Reconnected` evento. Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha restablecido y quitado de la cola todos los mensajes en cola.

Dado que la conexión es completamente nueva en el servidor, se `ConnectionId` proporcionará un nuevo a los `Reconnected` controladores de eventos.

> [!WARNING]
> El `Reconnected` parámetro del controlador de eventos será `connectionId` null si `HubConnection` se configuró para [omitir la negociación](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` no configurará el `HubConnection` para reintentar errores de inicio inicial, por lo que los errores de inicio deben controlarse manualmente:

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

Si el cliente no se vuelve a conectar correctamente en los cuatro primeros intentos, `HubConnection` cambiará al `Disconnected` Estado y desencadenará el <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento. Esto proporciona una oportunidad para intentar reiniciar la conexión manualmente o informar a los usuarios de que la conexión se ha perdido permanentemente.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Con el fin de configurar un número personalizado de intentos de reconexión antes de desconectar o cambiar el tiempo de reconexión, `WithAutomaticReconnect` acepta una matriz de números que representan el retraso en milisegundos que se debe esperar antes de iniciar cada intento de reconexión.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

En el ejemplo anterior `HubConnection` se configura para iniciar el intento de reconexión inmediatamente después de la pérdida de la conexión. Esto también se aplica a la configuración predeterminada.

Si se produce un error en el primer intento de reconexión, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.

Si se produce un error en el segundo intento de reconexión, el tercer intento de reconexión se iniciará en 10 segundos, lo que volverá a ser como la configuración predeterminada.

Después, el comportamiento personalizado difiere de nuevo del comportamiento predeterminado al detenerse después del tercer error de intento de reconexión. En la configuración predeterminada, se producirá un intento de reconexión más en otros 30 segundos.

Si desea tener un mayor control sobre la temporización y el número de intentos de reconexión automática, `WithAutomaticReconnect` acepta un objeto `IRetryPolicy` que implementa la interfaz, que tiene un único método denominado `NextRetryDelay` .

`NextRetryDelay` toma un único argumento con el tipo `RetryContext` . `RetryContext`Tiene tres propiedades: `PreviousRetryCount` , `ElapsedTime` y `RetryReason` , que son `long` , y, `TimeSpan` `Exception` respectivamente. Antes del primer intento de reconexión, `PreviousRetryCount` y serán cero y será `ElapsedTime` `RetryReason` la excepción que provocó la pérdida de la conexión. Después de cada intento de reintento erróneo, se `PreviousRetryCount` incrementará en uno, se `ElapsedTime` actualizará para reflejar la cantidad de tiempo empleado en la reconexión hasta el momento, y será `RetryReason` la excepción que provocó el último error en el intento de reconexión.

`NextRetryDelay` debe devolver un valor TimeSpan que representa el tiempo de espera antes del siguiente intento de reconexión o `null` si `HubConnection` debe dejar de volver a conectarse.

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

Como alternativa, puede escribir código que volverá a conectar el cliente manualmente, tal como se muestra en [reconexión manual](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Volver a conectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes de 3,0, el cliente de .NET para SignalR no se vuelve a conectar automáticamente. Debe escribir código que volverá a conectar el cliente manualmente.

::: moniker-end

Utilice el <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento para responder a una conexión perdida. Por ejemplo, puede que desee automatizar la reconexión.

El `Closed` evento requiere un delegado que devuelve `Task` , que permite que el código asincrónico se ejecute sin usar `async void` . Para satisfacer la firma del delegado en un `Closed` controlador de eventos que se ejecuta sincrónicamente, devuelva `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

La razón principal de la compatibilidad con Async es para que pueda reiniciar la conexión. Iniciar una conexión es una acción asincrónica.

En un `Closed` controlador que reinicie la conexión, considere la posibilidad de esperar algún retraso aleatorio para evitar la sobrecarga del servidor, como se muestra en el ejemplo siguiente:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Llamar a métodos de Hub desde el cliente

`InvokeAsync` llama a los métodos en el concentrador. Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `InvokeAsync` . SignalR es asincrónica, por lo que debe usar `async` y `await` al realizar las llamadas.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

El `InvokeAsync` método devuelve un `Task` que se completa cuando el método de servidor devuelve. El valor devuelto, si existe, se proporciona como resultado de `Task` . Cualquier excepción producida por el método en el servidor produce un error `Task` . Use `await` la sintaxis para esperar a que se complete el método de servidor y la `try...catch` sintaxis para controlar los errores.

El `SendAsync` método devuelve un `Task` que se completa cuando se envía el mensaje al servidor. No se proporciona ningún valor devuelto, ya que esto `Task` no espera hasta que se complete el método del servidor. Las excepciones producidas en el cliente mientras se envía el mensaje producen un error `Task` . Use `await` la `try...catch` sintaxis y para controlar los errores de envío.

> [!NOTE]
> La llamada a métodos de concentrador desde un cliente solo se admite cuando se usa el SignalR servicio de Azure en el modo *predeterminado* . Para obtener más información, consulte preguntas más frecuentes [(repositorio de github de Azure signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Llamar a métodos de cliente desde el concentrador

Defina los métodos que el concentrador llama mediante `connection.On` después de compilar, pero antes de iniciar la conexión.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

El código anterior en `connection.On` se ejecuta cuando el código del lado servidor lo llama mediante el `SendAsync` método.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Registro y control de errores

Controlar los errores con una instrucción try-catch. Inspeccione el `Exception` objeto para determinar la acción adecuada que se realizará después de que se produzca un error.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Recursos adicionales

* [Concentradores](xref:signalr/hubs)
* [Cliente de JavaScript](xref:signalr/javascript-client)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
* [Documentación sin servidor del servicio de Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
