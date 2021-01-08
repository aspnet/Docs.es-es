---
title: Usar el protocolo MessagePack Hub en SignalR para ASP.net Core
author: bradygaster
description: Agregue el protocolo MessagePack Hub a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024696"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="2ad14-103">Usar el protocolo MessagePack Hub en SignalR para ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2ad14-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2ad14-104">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ad14-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ad14-105">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ad14-105">What is MessagePack?</span></span>

<span data-ttu-id="2ad14-106">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ad14-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ad14-107">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ad14-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ad14-108">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ad14-109">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ad14-110">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ad14-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ad14-111">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ad14-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ad14-112">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-113">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ad14-113">JSON is enabled by default.</span></span> <span data-ttu-id="2ad14-114">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ad14-115">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ad14-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ad14-116">En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ad14-117">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ad14-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ad14-118">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ad14-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="2ad14-119">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ad14-120">Por ejemplo, al `.WithSecurity(MessagePackSecurity.UntrustedData)` reemplazar el `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ad14-121">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ad14-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-122">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ad14-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ad14-123">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ad14-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ad14-124">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ad14-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-125">.NET client</span></span>

<span data-ttu-id="2ad14-126">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-127">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ad14-128">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-128">JavaScript client</span></span>

<span data-ttu-id="2ad14-129">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ad14-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ad14-130">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ad14-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2ad14-131">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ad14-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ad14-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ad14-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2ad14-133">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ad14-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ad14-134">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ad14-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ad14-135">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-136">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ad14-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ad14-137">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ad14-138">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ad14-139">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-140">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="2ad14-141">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="2ad14-141">Java client</span></span>

<span data-ttu-id="2ad14-142">Para habilitar MessagePack con Java, instale el `com.microsoft.signalr.messagepack` paquete.</span><span class="sxs-lookup"><span data-stu-id="2ad14-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="2ad14-143">Al usar Gradle, agregue la siguiente línea a la `dependencies` sección del archivo *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="2ad14-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="2ad14-144">Al utilizar Maven, agregue las líneas siguientes dentro del `<dependencies>` elemento del archivo *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="2ad14-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="2ad14-145">Llame a `withHubProtocol(new MessagePackHubProtocol())` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="2ad14-146">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-146">MessagePack quirks</span></span>

<span data-ttu-id="2ad14-147">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ad14-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ad14-148">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ad14-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ad14-149">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ad14-150">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ad14-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ad14-151">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ad14-152">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ad14-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ad14-153">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ad14-154">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ad14-155">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ad14-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ad14-156">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ad14-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ad14-157">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ad14-158">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub se convertirá al formato UTC Si, de `DateTime.Kind` `DateTimeKind.Local` lo contrario, no tocará el tiempo y lo pasará tal cual.</span><span class="sxs-lookup"><span data-stu-id="2ad14-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="2ad14-159">Si está trabajando con `DateTime` valores, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ad14-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ad14-160">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ad14-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ad14-161">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ad14-162">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ad14-163">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ad14-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ad14-164">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ad14-165">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ad14-166">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ad14-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ad14-167">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ad14-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ad14-168">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ad14-169">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ad14-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ad14-170">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ad14-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ad14-171">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ad14-172">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ad14-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ad14-173">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ad14-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ad14-174">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="2ad14-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="2ad14-175">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ad14-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ad14-176">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ad14-177">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ad14-178">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ad14-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ad14-179">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ad14-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ad14-180">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ad14-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="2ad14-181">Caracteres y cadenas en Java</span><span class="sxs-lookup"><span data-stu-id="2ad14-181">Chars and Strings in Java</span></span>

<span data-ttu-id="2ad14-182">En el cliente de Java, los `char` objetos se serializarán como objetos de un solo carácter `String` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="2ad14-183">Esto contrasta con el cliente de C# y JavaScript, que los serializa como `short` objetos.</span><span class="sxs-lookup"><span data-stu-id="2ad14-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="2ad14-184">La especificación de MessagePack en sí no define el comportamiento de los `char` objetos, por lo que depende del autor de la biblioteca para determinar cómo serializarlos.</span><span class="sxs-lookup"><span data-stu-id="2ad14-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="2ad14-185">La diferencia de comportamiento entre nuestros clientes es el resultado de las bibliotecas que usamos para nuestras implementaciones.</span><span class="sxs-lookup"><span data-stu-id="2ad14-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ad14-186">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ad14-186">Related resources</span></span>

* [<span data-ttu-id="2ad14-187">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="2ad14-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ad14-188">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ad14-189">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2ad14-190">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ad14-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ad14-191">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ad14-191">What is MessagePack?</span></span>

<span data-ttu-id="2ad14-192">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ad14-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ad14-193">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ad14-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ad14-194">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ad14-195">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ad14-196">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ad14-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ad14-197">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ad14-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ad14-198">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-199">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ad14-199">JSON is enabled by default.</span></span> <span data-ttu-id="2ad14-200">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ad14-201">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ad14-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ad14-202">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ad14-203">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ad14-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ad14-204">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ad14-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="2ad14-205">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ad14-206">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2ad14-207">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2ad14-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2ad14-208">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="2ad14-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2ad14-209">`MessagePack` la versión 2. x presentó cambios importantes y es incompatible con SignalR las versiones 3,1 y anteriores.</span><span class="sxs-lookup"><span data-stu-id="2ad14-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="2ad14-210">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="2ad14-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2ad14-211">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ad14-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ad14-212">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ad14-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-213">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ad14-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ad14-214">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ad14-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ad14-215">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ad14-216">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-216">.NET client</span></span>

<span data-ttu-id="2ad14-217">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-218">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ad14-219">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-219">JavaScript client</span></span>

<span data-ttu-id="2ad14-220">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ad14-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ad14-221">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ad14-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2ad14-222">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ad14-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ad14-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ad14-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2ad14-224">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ad14-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ad14-225">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ad14-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ad14-226">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-227">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ad14-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ad14-228">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ad14-229">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ad14-230">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-231">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2ad14-232">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-232">MessagePack quirks</span></span>

<span data-ttu-id="2ad14-233">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ad14-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ad14-234">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ad14-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ad14-235">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ad14-236">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ad14-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ad14-237">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ad14-238">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ad14-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ad14-239">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ad14-240">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ad14-241">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ad14-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ad14-242">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ad14-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ad14-243">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ad14-244">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="2ad14-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2ad14-245">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ad14-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ad14-246">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ad14-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2ad14-247">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2ad14-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ad14-248">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ad14-249">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ad14-250">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ad14-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ad14-251">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ad14-252">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ad14-253">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ad14-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ad14-254">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ad14-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ad14-255">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ad14-256">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ad14-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ad14-257">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ad14-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ad14-258">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ad14-259">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ad14-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ad14-260">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ad14-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ad14-261">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2ad14-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2ad14-262">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ad14-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ad14-263">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ad14-264">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ad14-265">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ad14-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ad14-266">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ad14-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ad14-267">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ad14-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ad14-268">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ad14-268">Related resources</span></span>

* [<span data-ttu-id="2ad14-269">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="2ad14-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ad14-270">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ad14-271">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2ad14-272">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ad14-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ad14-273">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ad14-273">What is MessagePack?</span></span>

<span data-ttu-id="2ad14-274">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ad14-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ad14-275">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ad14-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ad14-276">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ad14-277">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ad14-278">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ad14-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ad14-279">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ad14-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ad14-280">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-281">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ad14-281">JSON is enabled by default.</span></span> <span data-ttu-id="2ad14-282">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ad14-283">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ad14-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ad14-284">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ad14-285">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ad14-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ad14-286">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ad14-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="2ad14-287">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ad14-288">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2ad14-289">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2ad14-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2ad14-290">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="2ad14-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2ad14-291">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="2ad14-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2ad14-292">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ad14-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ad14-293">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ad14-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-294">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ad14-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ad14-295">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ad14-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ad14-296">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ad14-297">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-297">.NET client</span></span>

<span data-ttu-id="2ad14-298">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-299">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ad14-300">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-300">JavaScript client</span></span>

<span data-ttu-id="2ad14-301">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ad14-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ad14-302">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ad14-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="2ad14-303">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ad14-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ad14-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ad14-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="2ad14-305">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ad14-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ad14-306">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ad14-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ad14-307">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ad14-308">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ad14-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ad14-309">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ad14-310">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ad14-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ad14-311">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ad14-312">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2ad14-313">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-313">MessagePack quirks</span></span>

<span data-ttu-id="2ad14-314">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ad14-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ad14-315">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ad14-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ad14-316">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ad14-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ad14-317">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ad14-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ad14-318">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ad14-319">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ad14-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ad14-320">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ad14-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ad14-321">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ad14-322">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ad14-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ad14-323">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ad14-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ad14-324">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ad14-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ad14-325">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="2ad14-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2ad14-326">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ad14-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ad14-327">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ad14-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2ad14-328">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2ad14-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ad14-329">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ad14-330">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ad14-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ad14-331">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ad14-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ad14-332">El valor de `DateTime.MinValue` es `January 1, 0001` que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ad14-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ad14-333">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ad14-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ad14-334">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ad14-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ad14-335">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ad14-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ad14-336">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ad14-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ad14-337">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ad14-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ad14-338">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ad14-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ad14-339">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ad14-340">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ad14-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ad14-341">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ad14-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ad14-342">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2ad14-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2ad14-343">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ad14-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ad14-344">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ad14-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ad14-345">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ad14-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ad14-346">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ad14-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ad14-347">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ad14-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ad14-348">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ad14-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ad14-349">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ad14-349">Related resources</span></span>

* [<span data-ttu-id="2ad14-350">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="2ad14-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ad14-351">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ad14-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ad14-352">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ad14-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
