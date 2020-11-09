---
title: 'Usar streaming en ASP.NET Core SignalR'
author: bradygaster
description: Obtenga información acerca de cómo transmitir datos entre el cliente y el servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/streaming
ms.openlocfilehash: b07c280f271ccdd525128b973da065001a5cf0ed
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062446"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="9cd99-103">Usar streaming en ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9cd99-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="9cd99-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="9cd99-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cd99-105">ASP.NET Core SignalR admite el streaming desde el cliente al servidor y desde el servidor al cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="9cd99-106">Esto resulta útil para escenarios en los que llegan fragmentos de datos a lo largo del tiempo.</span><span class="sxs-lookup"><span data-stu-id="9cd99-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="9cd99-107">Al transmitir por secuencias, cada fragmento se envía al cliente o al servidor en cuanto está disponible, en lugar de esperar a que todos los datos estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="9cd99-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9cd99-108">ASP.NET Core SignalR admite el streaming de valores devueltos de métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="9cd99-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="9cd99-109">Esto resulta útil para escenarios en los que llegan fragmentos de datos a lo largo del tiempo.</span><span class="sxs-lookup"><span data-stu-id="9cd99-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="9cd99-110">Cuando un valor devuelto se transmite al cliente, cada fragmento se envía al cliente en cuanto está disponible, en lugar de esperar a que todos los datos estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="9cd99-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="9cd99-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9cd99-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="9cd99-112">Configuración de un centro para la transmisión por secuencias</span><span class="sxs-lookup"><span data-stu-id="9cd99-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cd99-113">Un método de concentrador se convierte automáticamente en un método de concentrador de streaming cuando devuelve <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` o `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9cd99-114">Un método de concentrador se convierte automáticamente en un método de concentrador de streaming cuando devuelve <xref:System.Threading.Channels.ChannelReader%601> o `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="9cd99-115">Streaming de servidor a cliente</span><span class="sxs-lookup"><span data-stu-id="9cd99-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cd99-116">Los métodos de streaming Hub pueden devolver además de `IAsyncEnumerable<T>` `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="9cd99-117">La manera más sencilla de devolver `IAsyncEnumerable<T>` es hacer que el método de concentrador sea un método de iterador asincrónico como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="9cd99-118">Los métodos de iterador Async de concentrador pueden aceptar un `CancellationToken` parámetro que se desencadena cuando el cliente cancela la suscripción de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="9cd99-119">Los métodos de iterador asincrónico evitan problemas comunes con los canales, como no devolver el `ChannelReader` tiempo suficiente o salir del método sin completar el <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="9cd99-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="9cd99-120">En el ejemplo siguiente se muestran los aspectos básicos de la transmisión de datos al cliente mediante canales.</span><span class="sxs-lookup"><span data-stu-id="9cd99-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="9cd99-121">Siempre que se escribe un objeto en <xref:System.Threading.Channels.ChannelWriter%601> , el objeto se envía inmediatamente al cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="9cd99-122">Al final, `ChannelWriter` se completa para indicar al cliente que la secuencia está cerrada.</span><span class="sxs-lookup"><span data-stu-id="9cd99-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="9cd99-123">Escriba en `ChannelWriter<T>` en un subproceso en segundo plano y devuelva lo `ChannelReader` antes posible.</span><span class="sxs-lookup"><span data-stu-id="9cd99-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="9cd99-124">Otras invocaciones del concentrador se bloquean hasta que `ChannelReader` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="9cd99-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="9cd99-125">Ajuste la lógica en una [ `try ... catch` instrucción](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="9cd99-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="9cd99-126">Complete el `Channel` en un [ `finally` bloque](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="9cd99-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="9cd99-127">Si desea fluir un error, Capture dentro del `catch` bloque y escríbalo en el `finally` bloque.</span><span class="sxs-lookup"><span data-stu-id="9cd99-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9cd99-128">Los métodos del concentrador de streaming de servidor a cliente pueden aceptar un `CancellationToken` parámetro que se desencadena cuando el cliente cancela la suscripción a la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="9cd99-129">Use este token para detener la operación del servidor y liberar los recursos si el cliente se desconecta antes del final de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="9cd99-130">Streaming de cliente a servidor</span><span class="sxs-lookup"><span data-stu-id="9cd99-130">Client-to-server streaming</span></span>

<span data-ttu-id="9cd99-131">Un método de concentrador se convierte automáticamente en un método de concentrador de streaming de cliente a servidor cuando acepta uno o más objetos de tipo <xref:System.Threading.Channels.ChannelReader%601> o <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="9cd99-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="9cd99-132">En el ejemplo siguiente se muestran los aspectos básicos de la lectura de datos de streaming enviados desde el cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="9cd99-133">Cada vez que el cliente escribe en <xref:System.Threading.Channels.ChannelWriter%601> , los datos se escriben en `ChannelReader` en el servidor desde el que el método de concentrador está leyendo.</span><span class="sxs-lookup"><span data-stu-id="9cd99-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="9cd99-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>A continuación se muestra una versión del método.</span><span class="sxs-lookup"><span data-stu-id="9cd99-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="9cd99-135">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="9cd99-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="9cd99-136">Streaming de servidor a cliente</span><span class="sxs-lookup"><span data-stu-id="9cd99-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cd99-137">Los `StreamAsync` `StreamAsChannelAsync` métodos y en `HubConnection` se usan para invocar métodos de streaming de servidor a cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="9cd99-138">Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `StreamAsync` o `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="9cd99-139">El parámetro genérico en `StreamAsync<T>` y `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias.</span><span class="sxs-lookup"><span data-stu-id="9cd99-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="9cd99-140">Un objeto de tipo `IAsyncEnumerable<T>` o `ChannelReader<T>` se devuelve de la invocación de flujo y representa la secuencia en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="9cd99-141">Un `StreamAsync` ejemplo que devuelve `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="9cd99-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="9cd99-142">Un `StreamAsChannelAsync` ejemplo correspondiente que devuelve `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="9cd99-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9cd99-143">El `StreamAsChannelAsync` método on `HubConnection` se usa para invocar un método de streaming de servidor a cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="9cd99-144">Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="9cd99-145">El parámetro Generic en `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias.</span><span class="sxs-lookup"><span data-stu-id="9cd99-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="9cd99-146">`ChannelReader<T>`Se devuelve un objeto de la invocación de flujo y representa la secuencia en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="9cd99-147">El `StreamAsChannelAsync` método on `HubConnection` se usa para invocar un método de streaming de servidor a cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="9cd99-148">Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="9cd99-149">El parámetro Generic en `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias.</span><span class="sxs-lookup"><span data-stu-id="9cd99-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="9cd99-150">`ChannelReader<T>`Se devuelve un objeto de la invocación de flujo y representa la secuencia en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="9cd99-151">Streaming de cliente a servidor</span><span class="sxs-lookup"><span data-stu-id="9cd99-151">Client-to-server streaming</span></span>

<span data-ttu-id="9cd99-152">Hay dos maneras de invocar un método de concentrador de streaming de cliente a servidor desde el cliente .NET.</span><span class="sxs-lookup"><span data-stu-id="9cd99-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="9cd99-153">Puede pasar `IAsyncEnumerable<T>` o `ChannelReader` como un argumento a `SendAsync` , `InvokeAsync` o `StreamAsChannelAsync` , dependiendo del método de concentrador invocado.</span><span class="sxs-lookup"><span data-stu-id="9cd99-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="9cd99-154">Cada vez que se escriben datos en el `IAsyncEnumerable` `ChannelWriter` objeto o, el método de concentrador del servidor recibe un nuevo elemento con los datos del cliente.</span><span class="sxs-lookup"><span data-stu-id="9cd99-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="9cd99-155">Si se usa un `IAsyncEnumerable` objeto, el flujo termina después de que se cierre el método que devuelve los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="9cd99-156">O bien, si está usando un `ChannelWriter` , complete el canal con `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="9cd99-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="9cd99-157">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="9cd99-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="9cd99-158">Streaming de servidor a cliente</span><span class="sxs-lookup"><span data-stu-id="9cd99-158">Server-to-client streaming</span></span>

<span data-ttu-id="9cd99-159">Los clientes de JavaScript llaman a los métodos de streaming de servidor a cliente en los concentradores con `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="9cd99-160">El `stream` método acepta dos argumentos:</span><span class="sxs-lookup"><span data-stu-id="9cd99-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="9cd99-161">Nombre del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="9cd99-161">The name of the hub method.</span></span> <span data-ttu-id="9cd99-162">En el ejemplo siguiente, el nombre del método del concentrador es `Counter` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="9cd99-163">Argumentos definidos en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="9cd99-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="9cd99-164">En el ejemplo siguiente, los argumentos son un recuento del número de elementos de secuencia que se van a recibir y el retraso entre los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="9cd99-165">`connection.stream` Devuelve un `IStreamResult` , que contiene un `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="9cd99-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="9cd99-166">Pase `IStreamSubscriber` a `subscribe` y establezca las `next` `error` `complete` devoluciones de llamada, y para recibir notificaciones de la `stream` invocación.</span><span class="sxs-lookup"><span data-stu-id="9cd99-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="9cd99-167">Para finalizar la secuencia del cliente, llame al `dispose` método en el `ISubscription` que se devuelve desde el `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="9cd99-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="9cd99-168">La llamada a este método provoca la cancelación del `CancellationToken` parámetro del método de concentrador, si se proporcionó uno.</span><span class="sxs-lookup"><span data-stu-id="9cd99-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="9cd99-169">Para finalizar la secuencia del cliente, llame al `dispose` método en el `ISubscription` que se devuelve desde el `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="9cd99-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="9cd99-170">Streaming de cliente a servidor</span><span class="sxs-lookup"><span data-stu-id="9cd99-170">Client-to-server streaming</span></span>

<span data-ttu-id="9cd99-171">Los clientes de JavaScript llaman a métodos de streaming de cliente a servidor en los concentradores pasando un `Subject` como argumento a `send` , `invoke` o `stream` , dependiendo del método de concentrador invocado.</span><span class="sxs-lookup"><span data-stu-id="9cd99-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="9cd99-172">`Subject`Es una clase que tiene el aspecto de un `Subject` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="9cd99-173">Por ejemplo, en RxJS, puede usar la clase de [asunto](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="9cd99-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="9cd99-174">La llamada a `subject.next(item)` con un elemento escribe el elemento en la secuencia y el método de concentrador recibe el elemento en el servidor.</span><span class="sxs-lookup"><span data-stu-id="9cd99-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="9cd99-175">Para finalizar el flujo, llame a `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="9cd99-176">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="9cd99-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="9cd99-177">Streaming de servidor a cliente</span><span class="sxs-lookup"><span data-stu-id="9cd99-177">Server-to-client streaming</span></span>

<span data-ttu-id="9cd99-178">El SignalR cliente de Java usa el `stream` método para invocar métodos de streaming.</span><span class="sxs-lookup"><span data-stu-id="9cd99-178">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="9cd99-179">`stream` acepta tres o más argumentos:</span><span class="sxs-lookup"><span data-stu-id="9cd99-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="9cd99-180">Tipo esperado de los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="9cd99-181">Nombre del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="9cd99-181">The name of the hub method.</span></span>
* <span data-ttu-id="9cd99-182">Argumentos definidos en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="9cd99-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="9cd99-183">El `stream` método en `HubConnection` devuelve un objeto observable del tipo de elemento de secuencia.</span><span class="sxs-lookup"><span data-stu-id="9cd99-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="9cd99-184">El método del tipo observable `subscribe` es donde `onNext` , `onError` y `onCompleted` se definen los controladores.</span><span class="sxs-lookup"><span data-stu-id="9cd99-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

### <a name="client-to-server-streaming"></a><span data-ttu-id="9cd99-185">Streaming de cliente a servidor</span><span class="sxs-lookup"><span data-stu-id="9cd99-185">Client-to-server streaming</span></span>

<span data-ttu-id="9cd99-186">El SignalR cliente de Java puede llamar a métodos de streaming de cliente a servidor en los concentradores pasando un objeto [observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) como argumento a `send` , `invoke` o `stream` , dependiendo del método de concentrador invocado.</span><span class="sxs-lookup"><span data-stu-id="9cd99-186">The SignalR Java client can call client-to-server streaming methods on hubs by passing in an [Observable](https://rxjs-dev.firebaseapp.com/api/index/class/Observable) as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span>

```java
ReplaySubject<String> stream = ReplaySubject.create();
hubConnection.send("UploadStream", stream);
stream.onNext("FirstItem");
stream.onNext("SecondItem");
stream.onComplete();
```

<span data-ttu-id="9cd99-187">La llamada a `stream.onNext(item)` con un elemento escribe el elemento en la secuencia y el método de concentrador recibe el elemento en el servidor.</span><span class="sxs-lookup"><span data-stu-id="9cd99-187">Calling `stream.onNext(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="9cd99-188">Para finalizar el flujo, llame a `stream.onComplete()` .</span><span class="sxs-lookup"><span data-stu-id="9cd99-188">To end the stream, call `stream.onComplete()`.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9cd99-189">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9cd99-189">Additional resources</span></span>

* [<span data-ttu-id="9cd99-190">Concentradores</span><span class="sxs-lookup"><span data-stu-id="9cd99-190">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9cd99-191">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="9cd99-191">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9cd99-192">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="9cd99-192">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="9cd99-193">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="9cd99-193">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
