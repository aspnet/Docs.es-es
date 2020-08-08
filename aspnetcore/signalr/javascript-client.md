---
title: Cliente de ASP.NET Core SignalR JavaScript
author: bradygaster
description: Información general de ASP.NET Core SignalR cliente JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 04200ced41113eb9a68cefc2f485f2d3798476c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022347"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="c855f-103">Cliente de ASP.NET Core SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="c855f-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="c855f-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="c855f-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="c855f-105">La biblioteca de cliente de ASP.NET Core SignalR JavaScript permite a los desarrolladores llamar a código de concentrador de servidor.</span><span class="sxs-lookup"><span data-stu-id="c855f-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="c855f-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c855f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="c855f-107">Instalar el SignalR paquete de cliente</span><span class="sxs-lookup"><span data-stu-id="c855f-107">Install the SignalR client package</span></span>

<span data-ttu-id="c855f-108">La SignalR biblioteca de cliente de JavaScript se entrega como un paquete [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="c855f-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="c855f-109">En las secciones siguientes se describen diferentes formas de instalar la biblioteca de cliente de.</span><span class="sxs-lookup"><span data-stu-id="c855f-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="c855f-110">Instalación con NPM</span><span class="sxs-lookup"><span data-stu-id="c855f-110">Install with npm</span></span>

<span data-ttu-id="c855f-111">Si usa Visual Studio, ejecute los siguientes comandos desde la **consola del administrador de paquetes** en la carpeta raíz.</span><span class="sxs-lookup"><span data-stu-id="c855f-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="c855f-112">Para Visual Studio Code, ejecute los siguientes comandos desde el **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="c855f-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="c855f-113">NPM instala el contenido del paquete en la *carpeta \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="c855f-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c855f-114">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="c855f-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c855f-115">Copie el archivo de *signalr.js* en la carpeta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="c855f-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="c855f-116">NPM instala el contenido del paquete en la *carpeta \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="c855f-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c855f-117">Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="c855f-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c855f-118">Copie el archivo de *signalr.js* en la carpeta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="c855f-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="c855f-119">Haga referencia al SignalR cliente de JavaScript en el `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="c855f-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="c855f-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c855f-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="c855f-121">Uso de una Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="c855f-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="c855f-122">Para usar la biblioteca de cliente sin el requisito previo de NPM, haga referencia a una copia hospedada en CDN de la biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="c855f-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="c855f-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c855f-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="c855f-124">La biblioteca de cliente está disponible en las siguientes redes CDN:</span><span class="sxs-lookup"><span data-stu-id="c855f-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="c855f-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c855f-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="c855f-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c855f-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="c855f-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="c855f-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="c855f-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c855f-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="c855f-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c855f-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="c855f-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="c855f-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="c855f-131">Instalación con LibMan</span><span class="sxs-lookup"><span data-stu-id="c855f-131">Install with LibMan</span></span>

<span data-ttu-id="c855f-132">[LibMan](xref:client-side/libman/index) se puede usar para instalar archivos de biblioteca de cliente específicos desde la biblioteca de cliente hospedada en CDN.</span><span class="sxs-lookup"><span data-stu-id="c855f-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="c855f-133">Por ejemplo, agregue solo el archivo JavaScript reducida al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c855f-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="c855f-134">Para obtener más información sobre este enfoque, consulte [Agregar la SignalR biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="c855f-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="c855f-135">Conexión a un concentrador</span><span class="sxs-lookup"><span data-stu-id="c855f-135">Connect to a hub</span></span>

<span data-ttu-id="c855f-136">El código siguiente crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="c855f-137">El nombre del centro no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c855f-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="c855f-138">Conexiones entre orígenes</span><span class="sxs-lookup"><span data-stu-id="c855f-138">Cross-origin connections</span></span>

<span data-ttu-id="c855f-139">Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada.</span><span class="sxs-lookup"><span data-stu-id="c855f-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="c855f-140">Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.</span><span class="sxs-lookup"><span data-stu-id="c855f-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="c855f-141">Para evitar que un sitio malintencionado Lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="c855f-142">Para permitir una solicitud entre orígenes, habilítela en la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="c855f-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="c855f-143">Llamar a métodos de Hub desde el cliente</span><span class="sxs-lookup"><span data-stu-id="c855f-143">Call hub methods from client</span></span>

<span data-ttu-id="c855f-144">Los clientes de JavaScript llaman a métodos públicos en los concentradores a través del método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="c855f-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="c855f-145">El `invoke` método acepta dos argumentos:</span><span class="sxs-lookup"><span data-stu-id="c855f-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="c855f-146">Nombre del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c855f-146">The name of the hub method.</span></span> <span data-ttu-id="c855f-147">En el ejemplo siguiente, el nombre del método en el concentrador es `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="c855f-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="c855f-148">Cualquier argumento definido en el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="c855f-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="c855f-149">En el ejemplo siguiente, el nombre del argumento es `message` .</span><span class="sxs-lookup"><span data-stu-id="c855f-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="c855f-150">El código de ejemplo utiliza la sintaxis de la función de flecha que se admite en las versiones actuales de todos los exploradores principales, excepto Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="c855f-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="c855f-151">Si utiliza SignalR el servicio de Azure en *modo sin servidor*, no puede llamar a métodos de concentrador desde un cliente.</span><span class="sxs-lookup"><span data-stu-id="c855f-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="c855f-152">Para obtener más información, consulte la [ SignalR documentación del servicio](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="c855f-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="c855f-153">El `invoke` método devuelve un [compromiso](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c855f-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="c855f-154">`Promise`Se resuelve con el valor devuelto (si existe) cuando el método en el servidor devuelve.</span><span class="sxs-lookup"><span data-stu-id="c855f-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="c855f-155">Si el método en el servidor produce un error, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="c855f-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c855f-156">Use los `then` `catch` métodos y en el `Promise` propio para controlar estos casos (o la `await` sintaxis).</span><span class="sxs-lookup"><span data-stu-id="c855f-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="c855f-157">El `send` método devuelve un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="c855f-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="c855f-158">`Promise`Se resuelve cuando el mensaje se ha enviado al servidor.</span><span class="sxs-lookup"><span data-stu-id="c855f-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="c855f-159">Si se produce un error al enviar el mensaje, `Promise` se rechaza con el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="c855f-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c855f-160">Use los `then` `catch` métodos y en el `Promise` propio para controlar estos casos (o la `await` sintaxis).</span><span class="sxs-lookup"><span data-stu-id="c855f-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-161">El uso de `send` no espera hasta que el servidor haya recibido el mensaje.</span><span class="sxs-lookup"><span data-stu-id="c855f-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="c855f-162">Por lo tanto, no es posible devolver datos ni errores del servidor.</span><span class="sxs-lookup"><span data-stu-id="c855f-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="c855f-163">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="c855f-163">Call client methods from hub</span></span>

<span data-ttu-id="c855f-164">Para recibir mensajes desde el concentrador, defina un método mediante el método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) de la `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c855f-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="c855f-165">Nombre del método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c855f-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="c855f-166">En el ejemplo siguiente, el nombre del método es `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="c855f-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="c855f-167">Argumentos que el concentrador pasa al método.</span><span class="sxs-lookup"><span data-stu-id="c855f-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="c855f-168">En el ejemplo siguiente, el valor del argumento es `message` .</span><span class="sxs-lookup"><span data-stu-id="c855f-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="c855f-169">El código anterior en `connection.on` se ejecuta cuando el código del lado servidor lo llama mediante el método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="c855f-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="c855f-170">SignalRdetermina el método de cliente al que se debe llamar haciendo coincidir el nombre del método y los argumentos definidos en `SendAsync` y `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="c855f-170">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-171">Como procedimiento recomendado, llame al método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el `HubConnection` después de `on` .</span><span class="sxs-lookup"><span data-stu-id="c855f-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="c855f-172">Esto garantiza que los controladores se registren antes de que se reciban los mensajes.</span><span class="sxs-lookup"><span data-stu-id="c855f-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="c855f-173">Registro y control de errores</span><span class="sxs-lookup"><span data-stu-id="c855f-173">Error handling and logging</span></span>

<span data-ttu-id="c855f-174">Encadenar un `catch` método al final del `start` método para controlar los errores del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c855f-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="c855f-175">Use `console.error` para generar errores en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c855f-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="c855f-176">Configure el seguimiento del registro del lado cliente pasando un registrador y un tipo de evento al registro cuando se establezca la conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="c855f-177">Los mensajes se registran con el nivel de registro especificado y superior.</span><span class="sxs-lookup"><span data-stu-id="c855f-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="c855f-178">Los niveles de registro disponibles son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="c855f-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="c855f-179">`signalR.LogLevel.Error`: Mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="c855f-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="c855f-180">`Error`Solo registra mensajes.</span><span class="sxs-lookup"><span data-stu-id="c855f-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="c855f-181">`signalR.LogLevel.Warning`: Mensajes de advertencia sobre posibles errores.</span><span class="sxs-lookup"><span data-stu-id="c855f-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="c855f-182">Registros `Warning` y `Error` mensajes.</span><span class="sxs-lookup"><span data-stu-id="c855f-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c855f-183">`signalR.LogLevel.Information`: Mensajes de estado sin errores.</span><span class="sxs-lookup"><span data-stu-id="c855f-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="c855f-184">Registra `Information` `Warning` mensajes, y `Error` .</span><span class="sxs-lookup"><span data-stu-id="c855f-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c855f-185">`signalR.LogLevel.Trace`: Mensajes de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="c855f-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="c855f-186">Registra todo, incluidos los datos transportados entre el concentrador y el cliente.</span><span class="sxs-lookup"><span data-stu-id="c855f-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="c855f-187">Use el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="c855f-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="c855f-188">Los mensajes se registran en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="c855f-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="c855f-189">Volver a conectar clientes</span><span class="sxs-lookup"><span data-stu-id="c855f-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="c855f-190">Volver a conectar automáticamente</span><span class="sxs-lookup"><span data-stu-id="c855f-190">Automatically reconnect</span></span>

<span data-ttu-id="c855f-191">El cliente de JavaScript para SignalR puede configurarse para que se vuelva a conectar automáticamente mediante el `withAutomaticReconnect` método en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c855f-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="c855f-192">No se volverá a conectar automáticamente de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="c855f-193">Sin parámetros, `withAutomaticReconnect()` configura el cliente para que espere 0, 2, 10 y 30 segundos, respectivamente, antes de intentar cada intento de reconexión, deteniéndose después de cuatro intentos erróneos.</span><span class="sxs-lookup"><span data-stu-id="c855f-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="c855f-194">Antes de iniciar cualquier intento de reconexión, `HubConnection` cambiará al `HubConnectionState.Reconnecting` Estado de y activará sus `onreconnecting` devoluciones de llamada en lugar de pasar al `Disconnected` Estado y desencadenar sus `onclose` devoluciones de llamada como una `HubConnection` sin reconexión automática configurada.</span><span class="sxs-lookup"><span data-stu-id="c855f-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="c855f-195">Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c855f-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c855f-196">Si el cliente se vuelve a conectar correctamente dentro de los cuatro primeros intentos, `HubConnection` volverá a pasar al `Connected` Estado y activará sus `onreconnected` devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="c855f-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="c855f-197">Esto proporciona una oportunidad para informar a los usuarios de que se ha restablecido la conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="c855f-198">Dado que la conexión es completamente nueva en el servidor, se `connectionId` proporcionará un nuevo a la `onreconnected` devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="c855f-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="c855f-199">El `onreconnected` parámetro de la devolución de llamada `connectionId` no se definirá si `HubConnection` se configuró para [omitir la negociación](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="c855f-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c855f-200">`withAutomaticReconnect()`no configurará el `HubConnection` para reintentar errores de inicio inicial, por lo que los errores de inicio deben controlarse manualmente:</span><span class="sxs-lookup"><span data-stu-id="c855f-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="c855f-201">Si el cliente no se vuelve a conectar correctamente en los cuatro primeros intentos, `HubConnection` cambiará al `Disconnected` Estado y activará sus devoluciones de llamada [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="c855f-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="c855f-202">Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha perdido permanentemente y recomienda actualizar la página:</span><span class="sxs-lookup"><span data-stu-id="c855f-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c855f-203">Con el fin de configurar un número personalizado de intentos de reconexión antes de desconectar o cambiar el tiempo de reconexión, `withAutomaticReconnect` acepta una matriz de números que representan el retraso en milisegundos que se debe esperar antes de iniciar cada intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="c855f-204">En el ejemplo anterior `HubConnection` se configura para iniciar el intento de reconexión inmediatamente después de la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="c855f-205">Esto también se aplica a la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="c855f-206">Si se produce un error en el primer intento de reconexión, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c855f-207">Si se produce un error en el segundo intento de reconexión, el tercer intento de reconexión se iniciará en 10 segundos, lo que volverá a ser como la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="c855f-208">Después, el comportamiento personalizado difiere de nuevo del comportamiento predeterminado al detenerse después de que se produzca un error en el tercer intento de reconexión en lugar de intentar un intento de reconexión más en otros 30 segundos como lo haría en la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c855f-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c855f-209">Si desea tener un mayor control sobre la temporización y el número de intentos de reconexión automática, `withAutomaticReconnect` acepta un objeto `IRetryPolicy` que implementa la interfaz, que tiene un único método denominado `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="c855f-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="c855f-210">`nextRetryDelayInMilliseconds`toma un único argumento con el tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="c855f-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="c855f-211">`RetryContext`Tiene tres propiedades: `previousRetryCount` , `elapsedMilliseconds` y `retryReason` que son `number` , `number` y, `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="c855f-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="c855f-212">Antes del primer intento de reconexión, `previousRetryCount` y será `elapsedMilliseconds` cero, y será `retryReason` el error que provocó la pérdida de la conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="c855f-213">Después de cada intento de reintento erróneo, se `previousRetryCount` incrementará en uno, se `elapsedMilliseconds` actualizará para reflejar la cantidad de tiempo empleado en la reconexión hasta el momento en milisegundos y el `retryReason` será el error que provocó el último intento de reconexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="c855f-214">`nextRetryDelayInMilliseconds`debe devolver un número que represente el número de milisegundos que se va a esperar antes del siguiente intento de reconexión o `null` si `HubConnection` debe dejar de volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="c855f-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="c855f-215">Como alternativa, puede escribir código que volverá a conectar el cliente manualmente, tal como se muestra en [reconexión manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="c855f-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="c855f-216">Volver a conectar manualmente</span><span class="sxs-lookup"><span data-stu-id="c855f-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="c855f-217">Antes de 3,0, el cliente de JavaScript para SignalR no se vuelve a conectar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="c855f-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="c855f-218">Debe escribir código que volverá a conectar el cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="c855f-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="c855f-219">En el código siguiente se muestra un enfoque típico de reconexión manual:</span><span class="sxs-lookup"><span data-stu-id="c855f-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="c855f-220">Una función (en este caso, la `start` función) se crea para iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="c855f-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="c855f-221">Llame a la `start` función en el controlador de eventos de la conexión `onclose` .</span><span class="sxs-lookup"><span data-stu-id="c855f-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="c855f-222">Una implementación del mundo real usaría una interrupción exponencial o reintentará un número especificado de veces antes de abandonarlo.</span><span class="sxs-lookup"><span data-stu-id="c855f-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c855f-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c855f-223">Additional resources</span></span>

* [<span data-ttu-id="c855f-224">Referencia de API de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c855f-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="c855f-225">Tutorial de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c855f-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c855f-226">Tutorial de WebPack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="c855f-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="c855f-227">Concentradores</span><span class="sxs-lookup"><span data-stu-id="c855f-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c855f-228">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c855f-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c855f-229">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="c855f-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="c855f-230">Solicitudes entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="c855f-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="c855f-231">Documentación sin servidor del servicio de Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="c855f-231">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
