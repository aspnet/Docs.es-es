---
title: Usar el protocolo MessagePack Hub en SignalR para ASP.net Core
author: bradygaster
description: Agregue el protocolo MessagePack Hub a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ab9bd11e37182f5b24db5595d5d050f4cc0e32da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626654"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="92e85-103">Usar el protocolo MessagePack Hub en SignalR para ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="92e85-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="92e85-104">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="92e85-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92e85-105">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="92e85-105">What is MessagePack?</span></span>

<span data-ttu-id="92e85-106">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="92e85-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92e85-107">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="92e85-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92e85-108">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="92e85-109">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92e85-110">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="92e85-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="92e85-111">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="92e85-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92e85-112">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-113">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="92e85-113">JSON is enabled by default.</span></span> <span data-ttu-id="92e85-114">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92e85-115">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="92e85-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92e85-116">En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92e85-117">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="92e85-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92e85-118">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="92e85-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92e85-119">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="92e85-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92e85-120">Por ejemplo, al `.WithSecurity(MessagePackSecurity.UntrustedData)` reemplazar el `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="92e85-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92e85-121">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="92e85-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-122">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="92e85-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92e85-123">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="92e85-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="92e85-124">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="92e85-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92e85-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-125">.NET client</span></span>

<span data-ttu-id="92e85-126">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="92e85-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92e85-127">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92e85-128">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-128">JavaScript client</span></span>

<span data-ttu-id="92e85-129">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="92e85-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92e85-130">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="92e85-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="92e85-131">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="92e85-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92e85-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92e85-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="92e85-133">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="92e85-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92e85-134">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="92e85-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92e85-135">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-136">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="92e85-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92e85-137">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92e85-138">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92e85-139">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92e85-140">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92e85-141">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-141">MessagePack quirks</span></span>

<span data-ttu-id="92e85-142">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="92e85-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92e85-143">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="92e85-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92e85-144">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="92e85-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92e85-145">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="92e85-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92e85-146">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92e85-147">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="92e85-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92e85-148">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92e85-149">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92e85-150">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="92e85-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92e85-151">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="92e85-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92e85-152">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="92e85-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92e85-153">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub se convertirá al formato UTC Si, de `DateTime.Kind` `DateTimeKind.Local` lo contrario, no tocará el tiempo y lo pasará tal cual.</span><span class="sxs-lookup"><span data-stu-id="92e85-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="92e85-154">Si está trabajando con `DateTime` valores, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="92e85-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92e85-155">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="92e85-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92e85-156">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92e85-157">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92e85-158">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="92e85-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92e85-159">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="92e85-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92e85-160">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92e85-161">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="92e85-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92e85-162">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="92e85-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92e85-163">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="92e85-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92e85-164">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="92e85-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92e85-165">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="92e85-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92e85-166">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92e85-167">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="92e85-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92e85-168">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="92e85-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92e85-169">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="92e85-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="92e85-170">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="92e85-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92e85-171">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92e85-172">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92e85-173">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="92e85-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92e85-174">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="92e85-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92e85-175">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="92e85-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92e85-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="92e85-176">Related resources</span></span>

* [<span data-ttu-id="92e85-177">Introducción</span><span class="sxs-lookup"><span data-stu-id="92e85-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92e85-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92e85-179">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="92e85-180">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="92e85-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92e85-181">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="92e85-181">What is MessagePack?</span></span>

<span data-ttu-id="92e85-182">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="92e85-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92e85-183">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="92e85-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92e85-184">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="92e85-185">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92e85-186">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="92e85-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="92e85-187">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="92e85-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92e85-188">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-189">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="92e85-189">JSON is enabled by default.</span></span> <span data-ttu-id="92e85-190">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92e85-191">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="92e85-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92e85-192">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92e85-193">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="92e85-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92e85-194">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="92e85-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92e85-195">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="92e85-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92e85-196">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="92e85-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="92e85-197">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="92e85-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="92e85-198">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="92e85-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="92e85-199">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="92e85-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="92e85-200">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="92e85-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92e85-201">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="92e85-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-202">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="92e85-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92e85-203">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="92e85-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="92e85-204">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="92e85-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92e85-205">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-205">.NET client</span></span>

<span data-ttu-id="92e85-206">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="92e85-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92e85-207">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92e85-208">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-208">JavaScript client</span></span>

<span data-ttu-id="92e85-209">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="92e85-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92e85-210">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="92e85-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="92e85-211">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="92e85-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92e85-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92e85-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="92e85-213">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="92e85-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92e85-214">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="92e85-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92e85-215">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-216">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="92e85-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92e85-217">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92e85-218">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92e85-219">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92e85-220">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92e85-221">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-221">MessagePack quirks</span></span>

<span data-ttu-id="92e85-222">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="92e85-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92e85-223">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="92e85-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92e85-224">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="92e85-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92e85-225">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="92e85-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92e85-226">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92e85-227">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="92e85-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92e85-228">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92e85-229">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92e85-230">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="92e85-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92e85-231">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="92e85-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92e85-232">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="92e85-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92e85-233">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="92e85-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="92e85-234">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="92e85-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92e85-235">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="92e85-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="92e85-236">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="92e85-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92e85-237">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92e85-238">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92e85-239">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="92e85-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92e85-240">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="92e85-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92e85-241">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92e85-242">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="92e85-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92e85-243">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="92e85-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92e85-244">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="92e85-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92e85-245">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="92e85-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92e85-246">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="92e85-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92e85-247">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92e85-248">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="92e85-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92e85-249">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="92e85-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92e85-250">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="92e85-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="92e85-251">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="92e85-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92e85-252">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92e85-253">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92e85-254">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="92e85-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92e85-255">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="92e85-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92e85-256">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="92e85-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92e85-257">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="92e85-257">Related resources</span></span>

* [<span data-ttu-id="92e85-258">Introducción</span><span class="sxs-lookup"><span data-stu-id="92e85-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92e85-259">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92e85-260">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92e85-261">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="92e85-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92e85-262">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="92e85-262">What is MessagePack?</span></span>

<span data-ttu-id="92e85-263">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="92e85-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92e85-264">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="92e85-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92e85-265">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="92e85-266">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92e85-267">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="92e85-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="92e85-268">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="92e85-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92e85-269">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-270">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="92e85-270">JSON is enabled by default.</span></span> <span data-ttu-id="92e85-271">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92e85-272">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="92e85-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92e85-273">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92e85-274">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="92e85-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92e85-275">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="92e85-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92e85-276">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="92e85-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92e85-277">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="92e85-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="92e85-278">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="92e85-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="92e85-279">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="92e85-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="92e85-280">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="92e85-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="92e85-281">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="92e85-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92e85-282">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="92e85-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-283">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="92e85-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92e85-284">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="92e85-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="92e85-285">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="92e85-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92e85-286">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-286">.NET client</span></span>

<span data-ttu-id="92e85-287">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="92e85-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92e85-288">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92e85-289">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-289">JavaScript client</span></span>

<span data-ttu-id="92e85-290">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="92e85-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92e85-291">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="92e85-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="92e85-292">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="92e85-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92e85-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92e85-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="92e85-294">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="92e85-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92e85-295">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="92e85-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92e85-296">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92e85-297">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="92e85-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92e85-298">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92e85-299">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="92e85-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92e85-300">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="92e85-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92e85-301">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92e85-302">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-302">MessagePack quirks</span></span>

<span data-ttu-id="92e85-303">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="92e85-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92e85-304">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="92e85-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92e85-305">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="92e85-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92e85-306">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="92e85-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92e85-307">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92e85-308">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="92e85-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92e85-309">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="92e85-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92e85-310">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92e85-311">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="92e85-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92e85-312">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="92e85-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92e85-313">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="92e85-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92e85-314">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="92e85-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="92e85-315">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="92e85-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92e85-316">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="92e85-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="92e85-317">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="92e85-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92e85-318">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92e85-319">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="92e85-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92e85-320">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="92e85-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92e85-321">El valor de `DateTime.MinValue` es `January 1, 0001` que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="92e85-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92e85-322">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92e85-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92e85-323">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="92e85-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92e85-324">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="92e85-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92e85-325">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="92e85-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92e85-326">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="92e85-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92e85-327">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="92e85-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92e85-328">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92e85-329">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="92e85-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92e85-330">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="92e85-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92e85-331">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="92e85-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="92e85-332">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="92e85-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92e85-333">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="92e85-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92e85-334">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="92e85-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92e85-335">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="92e85-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92e85-336">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="92e85-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92e85-337">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="92e85-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92e85-338">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="92e85-338">Related resources</span></span>

* [<span data-ttu-id="92e85-339">Introducción</span><span class="sxs-lookup"><span data-stu-id="92e85-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92e85-340">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92e85-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92e85-341">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="92e85-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
