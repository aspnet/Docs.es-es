---
title: Usar el protocolo MessagePack Hub en SignalR para ASP.net Core
author: bradygaster
description: Agregue el protocolo MessagePack Hub a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
ms.openlocfilehash: 15ae8911e2ffba43c7bb885efd153d8b6803bc2a
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393683"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="2ea7f-103">Usar el protocolo MessagePack Hub en SignalR para ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2ea7f-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2ea7f-104">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ea7f-105">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ea7f-105">What is MessagePack?</span></span>

<span data-ttu-id="2ea7f-106">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ea7f-107">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ea7f-108">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ea7f-109">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ea7f-110">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ea7f-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ea7f-111">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ea7f-112">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-113">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-113">JSON is enabled by default.</span></span> <span data-ttu-id="2ea7f-114">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ea7f-115">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ea7f-116">En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ea7f-117">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ea7f-118">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2ea7f-119">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ea7f-120">Por ejemplo, al `.WithSecurity(MessagePackSecurity.UntrustedData)` reemplazar el `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ea7f-121">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ea7f-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-122">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ea7f-123">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ea7f-124">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ea7f-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-125">.NET client</span></span>

<span data-ttu-id="2ea7f-126">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-127">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ea7f-128">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-128">JavaScript client</span></span>

<span data-ttu-id="2ea7f-129">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ea7f-130">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2ea7f-131">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ea7f-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ea7f-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2ea7f-133">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ea7f-134">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ea7f-135">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-136">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ea7f-137">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ea7f-138">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ea7f-139">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-140">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2ea7f-141">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-141">MessagePack quirks</span></span>

<span data-ttu-id="2ea7f-142">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ea7f-143">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ea7f-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ea7f-144">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ea7f-145">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ea7f-146">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ea7f-147">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ea7f-148">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ea7f-149">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ea7f-150">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ea7f-151">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ea7f-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ea7f-152">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ea7f-153">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub se convertirá al formato UTC Si, de `DateTime.Kind` `DateTimeKind.Local` lo contrario, no tocará el tiempo y lo pasará tal cual.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="2ea7f-154">Si está trabajando con `DateTime` valores, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ea7f-155">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ea7f-156">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ea7f-157">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ea7f-158">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ea7f-159">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ea7f-160">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ea7f-161">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ea7f-162">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ea7f-163">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ea7f-164">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ea7f-165">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ea7f-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ea7f-166">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ea7f-167">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ea7f-168">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ea7f-169">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="2ea7f-170">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ea7f-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ea7f-171">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ea7f-172">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ea7f-173">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ea7f-174">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ea7f-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ea7f-175">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ea7f-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ea7f-176">Related resources</span></span>

* [<span data-ttu-id="2ea7f-177">Introducción</span><span class="sxs-lookup"><span data-stu-id="2ea7f-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ea7f-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ea7f-179">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2ea7f-180">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ea7f-181">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ea7f-181">What is MessagePack?</span></span>

<span data-ttu-id="2ea7f-182">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ea7f-183">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ea7f-184">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ea7f-185">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ea7f-186">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ea7f-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ea7f-187">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ea7f-188">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-189">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-189">JSON is enabled by default.</span></span> <span data-ttu-id="2ea7f-190">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ea7f-191">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ea7f-192">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ea7f-193">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ea7f-194">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2ea7f-195">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ea7f-196">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2ea7f-197">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2ea7f-198">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2ea7f-199">`MessagePack` la versión 2. x presentó cambios importantes y es incompatible con SignalR las versiones 3,1 y anteriores.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="2ea7f-200">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2ea7f-201">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ea7f-202">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ea7f-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-203">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ea7f-204">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ea7f-205">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ea7f-206">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-206">.NET client</span></span>

<span data-ttu-id="2ea7f-207">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-208">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ea7f-209">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-209">JavaScript client</span></span>

<span data-ttu-id="2ea7f-210">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ea7f-211">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2ea7f-212">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ea7f-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ea7f-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2ea7f-214">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ea7f-215">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ea7f-216">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-217">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ea7f-218">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ea7f-219">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-219">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ea7f-220">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-221">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2ea7f-222">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-222">MessagePack quirks</span></span>

<span data-ttu-id="2ea7f-223">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ea7f-224">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ea7f-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ea7f-225">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ea7f-226">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ea7f-227">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ea7f-228">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ea7f-229">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ea7f-230">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ea7f-231">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ea7f-232">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ea7f-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ea7f-233">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ea7f-234">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2ea7f-235">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ea7f-236">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2ea7f-237">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ea7f-238">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ea7f-239">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ea7f-240">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ea7f-241">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ea7f-242">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ea7f-243">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ea7f-244">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ea7f-245">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ea7f-246">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ea7f-247">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ea7f-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ea7f-248">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ea7f-249">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ea7f-250">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ea7f-251">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2ea7f-252">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ea7f-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ea7f-253">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ea7f-254">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ea7f-255">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ea7f-256">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ea7f-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ea7f-257">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ea7f-258">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ea7f-258">Related resources</span></span>

* [<span data-ttu-id="2ea7f-259">Introducción</span><span class="sxs-lookup"><span data-stu-id="2ea7f-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ea7f-260">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ea7f-261">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2ea7f-262">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ea7f-263">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ea7f-263">What is MessagePack?</span></span>

<span data-ttu-id="2ea7f-264">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2ea7f-265">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ea7f-266">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ea7f-267">SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ea7f-268">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="2ea7f-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ea7f-269">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ea7f-270">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-271">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-271">JSON is enabled by default.</span></span> <span data-ttu-id="2ea7f-272">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ea7f-273">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ea7f-274">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ea7f-275">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ea7f-276">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2ea7f-277">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2ea7f-278">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2ea7f-279">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2ea7f-280">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2ea7f-281">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2ea7f-282">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ea7f-283">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="2ea7f-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-284">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ea7f-285">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ea7f-286">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ea7f-287">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-287">.NET client</span></span>

<span data-ttu-id="2ea7f-288">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-289">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ea7f-290">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-290">JavaScript client</span></span>

<span data-ttu-id="2ea7f-291">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2ea7f-292">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="2ea7f-293">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2ea7f-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2ea7f-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="2ea7f-295">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ea7f-296">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ea7f-297">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7f-298">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ea7f-299">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js*, se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ea7f-300">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-300">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ea7f-301">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ea7f-302">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2ea7f-303">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-303">MessagePack quirks</span></span>

<span data-ttu-id="2ea7f-304">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2ea7f-305">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="2ea7f-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2ea7f-306">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2ea7f-307">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2ea7f-308">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2ea7f-309">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2ea7f-310">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2ea7f-311">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2ea7f-312">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2ea7f-313">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="2ea7f-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2ea7f-314">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2ea7f-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2ea7f-315">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2ea7f-316">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2ea7f-317">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2ea7f-318">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2ea7f-319">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2ea7f-320">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2ea7f-321">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2ea7f-322">El valor de `DateTime.MinValue` es `January 1, 0001` que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2ea7f-323">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2ea7f-324">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="2ea7f-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2ea7f-325">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2ea7f-326">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2ea7f-327">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2ea7f-328">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="2ea7f-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2ea7f-329">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2ea7f-330">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2ea7f-331">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2ea7f-332">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2ea7f-333">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2ea7f-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2ea7f-334">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="2ea7f-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2ea7f-335">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2ea7f-336">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="2ea7f-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2ea7f-337">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="2ea7f-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2ea7f-338">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2ea7f-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ea7f-339">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ea7f-339">Related resources</span></span>

* [<span data-ttu-id="2ea7f-340">Introducción</span><span class="sxs-lookup"><span data-stu-id="2ea7f-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ea7f-341">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7f-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ea7f-342">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7f-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
