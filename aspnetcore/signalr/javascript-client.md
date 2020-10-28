---
title: 'Cliente de ASP.NET Core :::no-loc(SignalR)::: JavaScript'
author: bradygaster
description: 'Información general de ASP.NET Core :::no-loc(SignalR)::: cliente JavaScript.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
no-loc:
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
uid: signalr/javascript-client
ms.openlocfilehash: be271272c952487fccc5136307c84fdf49391848
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690650"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="36685-103">Cliente de ASP.NET Core :::no-loc(SignalR)::: JavaScript</span><span class="sxs-lookup"><span data-stu-id="36685-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36685-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="36685-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="36685-105">La biblioteca de cliente de ASP.NET Core :::no-loc(SignalR)::: JavaScript permite a los desarrolladores llamar a código de concentrador de servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-105">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="36685-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36685-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="36685-107">Instalar el :::no-loc(SignalR)::: paquete de cliente</span><span class="sxs-lookup"><span data-stu-id="36685-107">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="36685-108">La :::no-loc(SignalR)::: biblioteca de cliente de JavaScript se entrega como un paquete [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="36685-108">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="36685-109">En las secciones siguientes se describen diferentes formas de instalar la biblioteca de cliente de.</span><span class="sxs-lookup"><span data-stu-id="36685-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="36685-110">Instalación con NPM</span><span class="sxs-lookup"><span data-stu-id="36685-110">Install with npm</span></span>

<span data-ttu-id="36685-111">En Visual Studio, ejecute los siguientes comandos desde la **consola del administrador de paquetes** en la carpeta raíz.</span><span class="sxs-lookup"><span data-stu-id="36685-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="36685-112">Para Visual Studio Code, ejecute los siguientes comandos desde el **terminal integrado** .</span><span class="sxs-lookup"><span data-stu-id="36685-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="36685-113">NPM instala el contenido del paquete en la *carpeta \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="36685-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="36685-114">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="36685-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="36685-115">Copie el archivo de *signalr.js* en la carpeta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="36685-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="36685-116">Haga referencia al :::no-loc(SignalR)::: cliente de JavaScript en el `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="36685-116">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="36685-117">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36685-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="36685-118">Uso de una Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="36685-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="36685-119">Para usar la biblioteca de cliente sin el requisito previo de NPM, haga referencia a una copia hospedada en CDN de la biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="36685-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36685-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="36685-121">La biblioteca de cliente está disponible en las siguientes redes CDN:</span><span class="sxs-lookup"><span data-stu-id="36685-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="36685-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="36685-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="36685-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="36685-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="36685-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="36685-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="36685-125">Instalación con LibMan</span><span class="sxs-lookup"><span data-stu-id="36685-125">Install with LibMan</span></span>

<span data-ttu-id="36685-126">[LibMan](xref:client-side/libman/index) se puede usar para instalar archivos de biblioteca de cliente específicos desde la biblioteca de cliente hospedada en CDN.</span><span class="sxs-lookup"><span data-stu-id="36685-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="36685-127">Por ejemplo, agregue solo el archivo JavaScript reducida al proyecto.</span><span class="sxs-lookup"><span data-stu-id="36685-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="36685-128">Para obtener más información sobre este enfoque, consulte [Agregar la :::no-loc(SignalR)::: biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="36685-128">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="36685-129">Conexión a un concentrador</span><span class="sxs-lookup"><span data-stu-id="36685-129">Connect to a hub</span></span>

<span data-ttu-id="36685-130">El código siguiente crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="36685-131">El nombre del centro no distingue mayúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="36685-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="36685-132">Conexiones entre orígenes</span><span class="sxs-lookup"><span data-stu-id="36685-132">Cross-origin connections</span></span>

<span data-ttu-id="36685-133">Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada.</span><span class="sxs-lookup"><span data-stu-id="36685-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="36685-134">Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.</span><span class="sxs-lookup"><span data-stu-id="36685-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="36685-135">Para evitar que un sitio malintencionado Lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="36685-136">Para permitir una solicitud entre orígenes, habilítela en la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="36685-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="36685-137">Llamar a métodos de Hub desde el cliente</span><span class="sxs-lookup"><span data-stu-id="36685-137">Call hub methods from the client</span></span>

<span data-ttu-id="36685-138">Los clientes de JavaScript llaman a métodos públicos en los concentradores a través del método [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) de [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="36685-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="36685-139">El `invoke` método acepta:</span><span class="sxs-lookup"><span data-stu-id="36685-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="36685-140">Nombre del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="36685-140">The name of the hub method.</span></span>
* <span data-ttu-id="36685-141">Cualquier argumento definido en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="36685-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="36685-142">En el ejemplo siguiente, el nombre del método en el concentrador es `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="36685-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="36685-143">Los argumentos segundo y tercero pasados a `invoke` la asignación a los argumentos y del método del concentrador `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="36685-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="36685-144">La llamada a métodos de concentrador desde un cliente solo se admite cuando se usa el :::no-loc(SignalR)::: servicio de Azure en el modo *predeterminado* .</span><span class="sxs-lookup"><span data-stu-id="36685-144">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="36685-145">Para obtener más información, consulte preguntas más frecuentes [(repositorio de github de Azure signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="36685-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="36685-146">El `invoke` método devuelve un [compromiso](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36685-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="36685-147">`Promise`Se resuelve con el valor devuelto (si existe) cuando el método en el servidor devuelve.</span><span class="sxs-lookup"><span data-stu-id="36685-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="36685-148">Si el método en el servidor produce un error, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="36685-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36685-149">Use `async` y `await` o los `Promise` `then` métodos y de `catch` para controlar estos casos.</span><span class="sxs-lookup"><span data-stu-id="36685-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="36685-150">Los clientes de JavaScript también pueden llamar a métodos públicos en los concentradores a través del método [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) de `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="36685-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="36685-151">A diferencia del `invoke` método, el `send` método no espera una respuesta del servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="36685-152">El `send` método devuelve un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="36685-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="36685-153">`Promise`Se resuelve cuando el mensaje se ha enviado al servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="36685-154">Si se produce un error al enviar el mensaje, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="36685-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36685-155">Use `async` y `await` o los `Promise` `then` métodos y de `catch` para controlar estos casos.</span><span class="sxs-lookup"><span data-stu-id="36685-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="36685-156">El uso de `send` no espera hasta que el servidor haya recibido el mensaje.</span><span class="sxs-lookup"><span data-stu-id="36685-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="36685-157">Por lo tanto, no es posible devolver datos ni errores del servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="36685-158">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="36685-158">Call client methods from the hub</span></span>

<span data-ttu-id="36685-159">Para recibir mensajes desde el concentrador, defina un método mediante el método [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) de la `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="36685-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="36685-160">Nombre del método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36685-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="36685-161">Argumentos que el concentrador pasa al método.</span><span class="sxs-lookup"><span data-stu-id="36685-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="36685-162">En el ejemplo siguiente, el nombre del método es `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="36685-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="36685-163">Los nombres de argumento son `user` y `message` :</span><span class="sxs-lookup"><span data-stu-id="36685-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="36685-164">El código anterior en `connection.on` se ejecuta cuando el código del lado servidor lo llama mediante el <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> método:</span><span class="sxs-lookup"><span data-stu-id="36685-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="36685-165">:::no-loc(SignalR)::: determina el método de cliente al que se debe llamar haciendo coincidir el nombre del método y los argumentos definidos en `SendAsync` y `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="36685-165">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="36685-166">Como procedimiento recomendado, llame al método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el `HubConnection` después de `on` .</span><span class="sxs-lookup"><span data-stu-id="36685-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="36685-167">Esto garantiza que los controladores se registren antes de que se reciban los mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="36685-168">Registro y control de errores</span><span class="sxs-lookup"><span data-stu-id="36685-168">Error handling and logging</span></span>

<span data-ttu-id="36685-169">Use `try` y `catch` con `async` y, `await` o `Promise` el `catch` método de para controlar los errores del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="36685-170">Use `console.error` para generar errores en la consola del explorador:</span><span class="sxs-lookup"><span data-stu-id="36685-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="36685-171">Configure el seguimiento del registro del lado cliente pasando un registrador y un tipo de evento al registro cuando se establezca la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="36685-172">Los mensajes se registran con el nivel de registro especificado y superior.</span><span class="sxs-lookup"><span data-stu-id="36685-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="36685-173">Los niveles de registro disponibles son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="36685-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="36685-174">`signalR.LogLevel.Error`: Mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="36685-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="36685-175">`Error`Solo registra mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="36685-176">`signalR.LogLevel.Warning`: Mensajes de advertencia sobre posibles errores.</span><span class="sxs-lookup"><span data-stu-id="36685-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="36685-177">Registros `Warning` y `Error` mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36685-178">`signalR.LogLevel.Information`: Mensajes de estado sin errores.</span><span class="sxs-lookup"><span data-stu-id="36685-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="36685-179">Registra `Information` `Warning` mensajes, y `Error` .</span><span class="sxs-lookup"><span data-stu-id="36685-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36685-180">`signalR.LogLevel.Trace`: Mensajes de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="36685-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="36685-181">Registra todo, incluidos los datos transportados entre el concentrador y el cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="36685-182">Use el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="36685-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="36685-183">Los mensajes se registran en la consola del explorador:</span><span class="sxs-lookup"><span data-stu-id="36685-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="36685-184">Volver a conectar clientes</span><span class="sxs-lookup"><span data-stu-id="36685-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="36685-185">Volver a conectar automáticamente</span><span class="sxs-lookup"><span data-stu-id="36685-185">Automatically reconnect</span></span>

<span data-ttu-id="36685-186">El cliente de JavaScript para :::no-loc(SignalR)::: puede configurarse para que se vuelva a conectar automáticamente mediante el `withAutomaticReconnect` método en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="36685-186">The JavaScript client for :::no-loc(SignalR)::: can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="36685-187">No se volverá a conectar automáticamente de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="36685-188">Sin parámetros, `withAutomaticReconnect()` configura el cliente para que espere 0, 2, 10 y 30 segundos, respectivamente, antes de intentar cada intento de reconexión, deteniéndose después de cuatro intentos erróneos.</span><span class="sxs-lookup"><span data-stu-id="36685-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="36685-189">Antes de iniciar cualquier intento de reconexión, `HubConnection` cambiará al `HubConnectionState.Reconnecting` Estado de y activará sus `onreconnecting` devoluciones de llamada en lugar de pasar al `Disconnected` Estado y desencadenar sus `onclose` devoluciones de llamada como una `HubConnection` sin reconexión automática configurada.</span><span class="sxs-lookup"><span data-stu-id="36685-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="36685-190">Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="36685-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36685-191">Si el cliente se vuelve a conectar correctamente dentro de los cuatro primeros intentos, `HubConnection` volverá a pasar al `Connected` Estado y activará sus `onreconnected` devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="36685-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="36685-192">Esto proporciona una oportunidad para informar a los usuarios de que se ha restablecido la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="36685-193">Dado que la conexión es completamente nueva en el servidor, se `connectionId` proporcionará un nuevo a la `onreconnected` devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="36685-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="36685-194">El `onreconnected` parámetro de la devolución de llamada `connectionId` no se definirá si `HubConnection` se configuró para [omitir la negociación](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="36685-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36685-195">`withAutomaticReconnect()` no configurará el `HubConnection` para reintentar errores de inicio inicial, por lo que los errores de inicio deben controlarse manualmente:</span><span class="sxs-lookup"><span data-stu-id="36685-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log(":::no-loc(SignalR)::: Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="36685-196">Si el cliente no se vuelve a conectar correctamente en los cuatro primeros intentos, `HubConnection` cambiará al `Disconnected` Estado y activará sus devoluciones de llamada [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="36685-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="36685-197">Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha perdido permanentemente y recomienda actualizar la página:</span><span class="sxs-lookup"><span data-stu-id="36685-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36685-198">Con el fin de configurar un número personalizado de intentos de reconexión antes de desconectar o cambiar el tiempo de reconexión, `withAutomaticReconnect` acepta una matriz de números que representan el retraso en milisegundos que se debe esperar antes de iniciar cada intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="36685-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="36685-199">En el ejemplo anterior `HubConnection` se configura para iniciar el intento de reconexión inmediatamente después de la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="36685-200">Esto también se aplica a la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="36685-201">Si se produce un error en el primer intento de reconexión, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="36685-202">Si se produce un error en el segundo intento de reconexión, el tercer intento de reconexión se iniciará en 10 segundos, lo que volverá a ser como la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="36685-203">Después, el comportamiento personalizado difiere de nuevo del comportamiento predeterminado al detenerse después de que se produzca un error en el tercer intento de reconexión en lugar de intentar un intento de reconexión más en otros 30 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="36685-204">Si desea tener un mayor control sobre la temporización y el número de intentos de reconexión automática, `withAutomaticReconnect` acepta un objeto `IRetryPolicy` que implementa la interfaz, que tiene un único método denominado `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="36685-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="36685-205">`nextRetryDelayInMilliseconds` toma un único argumento con el tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="36685-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="36685-206">`RetryContext`Tiene tres propiedades: `previousRetryCount` , `elapsedMilliseconds` y `retryReason` que son `number` , `number` y, `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="36685-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="36685-207">Antes del primer intento de reconexión, `previousRetryCount` y será `elapsedMilliseconds` cero, y será `retryReason` el error que provocó la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="36685-208">Después de cada intento de reintento erróneo, se `previousRetryCount` incrementará en uno, se `elapsedMilliseconds` actualizará para reflejar la cantidad de tiempo empleado en la reconexión hasta el momento en milisegundos y el `retryReason` será el error que provocó el último intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="36685-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="36685-209">`nextRetryDelayInMilliseconds` debe devolver un número que represente el número de milisegundos que se va a esperar antes del siguiente intento de reconexión o `null` si `HubConnection` debe dejar de volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="36685-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="36685-210">Como alternativa, puede escribir código que volverá a conectar el cliente manualmente, tal como se muestra en [reconexión manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="36685-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="36685-211">Volver a conectar manualmente</span><span class="sxs-lookup"><span data-stu-id="36685-211">Manually reconnect</span></span>

<span data-ttu-id="36685-212">En el código siguiente se muestra un enfoque típico de reconexión manual:</span><span class="sxs-lookup"><span data-stu-id="36685-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="36685-213">Una función (en este caso, la `start` función) se crea para iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="36685-214">Llame a la `start` función en el controlador de eventos de la conexión `onclose` .</span><span class="sxs-lookup"><span data-stu-id="36685-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="36685-215">Una implementación del mundo real usaría una interrupción exponencial o reintentará un número especificado de veces antes de abandonarlo.</span><span class="sxs-lookup"><span data-stu-id="36685-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36685-216">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="36685-216">Additional resources</span></span>

* [<span data-ttu-id="36685-217">Referencia de API de JavaScript</span><span class="sxs-lookup"><span data-stu-id="36685-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="36685-218">Tutorial de JavaScript</span><span class="sxs-lookup"><span data-stu-id="36685-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="36685-219">Tutorial de WebPack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="36685-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="36685-220">Concentradores</span><span class="sxs-lookup"><span data-stu-id="36685-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="36685-221">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="36685-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="36685-222">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="36685-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="36685-223">Solicitudes entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="36685-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="36685-224">Documentación sin servidor del servicio de Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="36685-224">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="36685-225">Solución de problemas de conexión</span><span class="sxs-lookup"><span data-stu-id="36685-225">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36685-226">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="36685-226">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="36685-227">La biblioteca de cliente de ASP.NET Core :::no-loc(SignalR)::: JavaScript permite a los desarrolladores llamar a código de concentrador de servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-227">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="36685-228">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36685-228">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="36685-229">Instalar el :::no-loc(SignalR)::: paquete de cliente</span><span class="sxs-lookup"><span data-stu-id="36685-229">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="36685-230">La :::no-loc(SignalR)::: biblioteca de cliente de JavaScript se entrega como un paquete [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="36685-230">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="36685-231">En las secciones siguientes se describen diferentes formas de instalar la biblioteca de cliente de.</span><span class="sxs-lookup"><span data-stu-id="36685-231">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="36685-232">Instalación con NPM</span><span class="sxs-lookup"><span data-stu-id="36685-232">Install with npm</span></span>

<span data-ttu-id="36685-233">Si usa Visual Studio, ejecute los siguientes comandos desde la **consola del administrador de paquetes** en la carpeta raíz.</span><span class="sxs-lookup"><span data-stu-id="36685-233">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="36685-234">Para Visual Studio Code, ejecute los siguientes comandos desde el **terminal integrado** .</span><span class="sxs-lookup"><span data-stu-id="36685-234">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="36685-235">NPM instala el contenido del paquete en la *carpeta \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="36685-235">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="36685-236">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="36685-236">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="36685-237">Copie el archivo de *signalr.js* en la carpeta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="36685-237">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="36685-238">Haga referencia al :::no-loc(SignalR)::: cliente de JavaScript en el `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="36685-238">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="36685-239">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36685-239">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="36685-240">Uso de una Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="36685-240">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="36685-241">Para usar la biblioteca de cliente sin el requisito previo de NPM, haga referencia a una copia hospedada en CDN de la biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-241">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="36685-242">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36685-242">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="36685-243">La biblioteca de cliente está disponible en las siguientes redes CDN:</span><span class="sxs-lookup"><span data-stu-id="36685-243">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="36685-244">cdnjs</span><span class="sxs-lookup"><span data-stu-id="36685-244">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="36685-245">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="36685-245">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="36685-246">unpkg</span><span class="sxs-lookup"><span data-stu-id="36685-246">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="36685-247">Instalación con LibMan</span><span class="sxs-lookup"><span data-stu-id="36685-247">Install with LibMan</span></span>

<span data-ttu-id="36685-248">[LibMan](xref:client-side/libman/index) se puede usar para instalar archivos de biblioteca de cliente específicos desde la biblioteca de cliente hospedada en CDN.</span><span class="sxs-lookup"><span data-stu-id="36685-248">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="36685-249">Por ejemplo, agregue solo el archivo JavaScript reducida al proyecto.</span><span class="sxs-lookup"><span data-stu-id="36685-249">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="36685-250">Para obtener más información sobre este enfoque, consulte [Agregar la :::no-loc(SignalR)::: biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="36685-250">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="36685-251">Conexión a un concentrador</span><span class="sxs-lookup"><span data-stu-id="36685-251">Connect to a hub</span></span>

<span data-ttu-id="36685-252">El código siguiente crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-252">The following code creates and starts a connection.</span></span> <span data-ttu-id="36685-253">El nombre del centro no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36685-253">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="36685-254">Conexiones entre orígenes</span><span class="sxs-lookup"><span data-stu-id="36685-254">Cross-origin connections</span></span>

<span data-ttu-id="36685-255">Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada.</span><span class="sxs-lookup"><span data-stu-id="36685-255">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="36685-256">Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.</span><span class="sxs-lookup"><span data-stu-id="36685-256">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="36685-257">Para evitar que un sitio malintencionado Lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36685-257">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="36685-258">Para permitir una solicitud entre orígenes, habilítela en la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="36685-258">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="36685-259">Llamar a métodos de Hub desde el cliente</span><span class="sxs-lookup"><span data-stu-id="36685-259">Call hub methods from client</span></span>

<span data-ttu-id="36685-260">Los clientes de JavaScript llaman a métodos públicos en los concentradores a través del método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="36685-260">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="36685-261">El `invoke` método acepta dos argumentos:</span><span class="sxs-lookup"><span data-stu-id="36685-261">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="36685-262">Nombre del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="36685-262">The name of the hub method.</span></span> <span data-ttu-id="36685-263">En el ejemplo siguiente, el nombre del método en el concentrador es `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="36685-263">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="36685-264">Cualquier argumento definido en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="36685-264">Any arguments defined in the hub method.</span></span> <span data-ttu-id="36685-265">En el ejemplo siguiente, el nombre del argumento es `message` .</span><span class="sxs-lookup"><span data-stu-id="36685-265">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="36685-266">El código de ejemplo utiliza la sintaxis de la función de flecha que se admite en las versiones actuales de todos los exploradores principales, excepto Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="36685-266">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="36685-267">La llamada a métodos de concentrador desde un cliente solo se admite cuando se usa el :::no-loc(SignalR)::: servicio de Azure en el modo *predeterminado* .</span><span class="sxs-lookup"><span data-stu-id="36685-267">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="36685-268">Para obtener más información, consulte preguntas más frecuentes [(repositorio de github de Azure signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="36685-268">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="36685-269">El `invoke` método devuelve un [compromiso](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36685-269">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="36685-270">`Promise`Se resuelve con el valor devuelto (si existe) cuando el método en el servidor devuelve.</span><span class="sxs-lookup"><span data-stu-id="36685-270">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="36685-271">Si el método en el servidor produce un error, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="36685-271">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36685-272">Use los `then` `catch` métodos y en el `Promise` propio para controlar estos casos (o la `await` sintaxis).</span><span class="sxs-lookup"><span data-stu-id="36685-272">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="36685-273">El `send` método devuelve un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="36685-273">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="36685-274">`Promise`Se resuelve cuando el mensaje se ha enviado al servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-274">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="36685-275">Si se produce un error al enviar el mensaje, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="36685-275">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36685-276">Use los `then` `catch` métodos y en el `Promise` propio para controlar estos casos (o la `await` sintaxis).</span><span class="sxs-lookup"><span data-stu-id="36685-276">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="36685-277">El uso de `send` no espera hasta que el servidor haya recibido el mensaje.</span><span class="sxs-lookup"><span data-stu-id="36685-277">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="36685-278">Por lo tanto, no es posible devolver datos ni errores del servidor.</span><span class="sxs-lookup"><span data-stu-id="36685-278">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="36685-279">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="36685-279">Call client methods from hub</span></span>

<span data-ttu-id="36685-280">Para recibir mensajes desde el concentrador, defina un método mediante el método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) de la `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="36685-280">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="36685-281">Nombre del método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36685-281">The name of the JavaScript client method.</span></span> <span data-ttu-id="36685-282">En el ejemplo siguiente, el nombre del método es `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="36685-282">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="36685-283">Argumentos que el concentrador pasa al método.</span><span class="sxs-lookup"><span data-stu-id="36685-283">Arguments the hub passes to the method.</span></span> <span data-ttu-id="36685-284">En el ejemplo siguiente, el valor del argumento es `message` .</span><span class="sxs-lookup"><span data-stu-id="36685-284">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="36685-285">El código anterior en `connection.on` se ejecuta cuando el código del lado servidor lo llama mediante el <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> método.</span><span class="sxs-lookup"><span data-stu-id="36685-285">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="36685-286">:::no-loc(SignalR)::: determina el método de cliente al que se debe llamar haciendo coincidir el nombre del método y los argumentos definidos en `SendAsync` y `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="36685-286">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="36685-287">Como procedimiento recomendado, llame al método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el `HubConnection` después de `on` .</span><span class="sxs-lookup"><span data-stu-id="36685-287">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="36685-288">Esto garantiza que los controladores se registren antes de que se reciban los mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-288">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="36685-289">Registro y control de errores</span><span class="sxs-lookup"><span data-stu-id="36685-289">Error handling and logging</span></span>

<span data-ttu-id="36685-290">Encadenar un `catch` método al final del `start` método para controlar los errores del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-290">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="36685-291">Use `console.error` para generar errores en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="36685-291">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="36685-292">Configure el seguimiento del registro del lado cliente pasando un registrador y un tipo de evento al registro cuando se establezca la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-292">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="36685-293">Los mensajes se registran con el nivel de registro especificado y superior.</span><span class="sxs-lookup"><span data-stu-id="36685-293">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="36685-294">Los niveles de registro disponibles son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="36685-294">Available log levels are as follows:</span></span>

* <span data-ttu-id="36685-295">`signalR.LogLevel.Error`: Mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="36685-295">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="36685-296">`Error`Solo registra mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-296">Logs `Error` messages only.</span></span>
* <span data-ttu-id="36685-297">`signalR.LogLevel.Warning`: Mensajes de advertencia sobre posibles errores.</span><span class="sxs-lookup"><span data-stu-id="36685-297">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="36685-298">Registros `Warning` y `Error` mensajes.</span><span class="sxs-lookup"><span data-stu-id="36685-298">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36685-299">`signalR.LogLevel.Information`: Mensajes de estado sin errores.</span><span class="sxs-lookup"><span data-stu-id="36685-299">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="36685-300">Registra `Information` `Warning` mensajes, y `Error` .</span><span class="sxs-lookup"><span data-stu-id="36685-300">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36685-301">`signalR.LogLevel.Trace`: Mensajes de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="36685-301">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="36685-302">Registra todo, incluidos los datos transportados entre el concentrador y el cliente.</span><span class="sxs-lookup"><span data-stu-id="36685-302">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="36685-303">Use el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="36685-303">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="36685-304">Los mensajes se registran en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="36685-304">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="36685-305">Volver a conectar clientes</span><span class="sxs-lookup"><span data-stu-id="36685-305">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="36685-306">Volver a conectar manualmente</span><span class="sxs-lookup"><span data-stu-id="36685-306">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="36685-307">Antes de 3,0, el cliente de JavaScript para :::no-loc(SignalR)::: no se vuelve a conectar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="36685-307">Prior to 3.0, the JavaScript client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="36685-308">Debe escribir código que volverá a conectar el cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="36685-308">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="36685-309">En el código siguiente se muestra un enfoque típico de reconexión manual:</span><span class="sxs-lookup"><span data-stu-id="36685-309">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="36685-310">Una función (en este caso, la `start` función) se crea para iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="36685-310">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="36685-311">Llame a la `start` función en el controlador de eventos de la conexión `onclose` .</span><span class="sxs-lookup"><span data-stu-id="36685-311">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="36685-312">Una implementación del mundo real usaría una interrupción exponencial o reintentará un número especificado de veces antes de abandonarlo.</span><span class="sxs-lookup"><span data-stu-id="36685-312">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36685-313">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="36685-313">Additional resources</span></span>

* [<span data-ttu-id="36685-314">Referencia de API de JavaScript</span><span class="sxs-lookup"><span data-stu-id="36685-314">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="36685-315">Tutorial de JavaScript</span><span class="sxs-lookup"><span data-stu-id="36685-315">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="36685-316">Tutorial de WebPack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="36685-316">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="36685-317">Concentradores</span><span class="sxs-lookup"><span data-stu-id="36685-317">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="36685-318">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="36685-318">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="36685-319">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="36685-319">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="36685-320">Solicitudes entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="36685-320">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="36685-321">Documentación sin servidor del servicio de Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="36685-321">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
