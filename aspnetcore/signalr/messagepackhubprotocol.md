---
title: 'Usar el protocolo MessagePack Hub en :::no-loc(SignalR)::: para ASP.net Core'
author: bradygaster
description: 'Agregue el protocolo MessagePack Hub a ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058173"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="ac3eb-103">Usar el protocolo MessagePack Hub en :::no-loc(SignalR)::: para ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ac3eb-103">Use MessagePack Hub Protocol in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ac3eb-104">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ac3eb-105">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ac3eb-105">What is MessagePack?</span></span>

<span data-ttu-id="ac3eb-106">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ac3eb-107">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ac3eb-108">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ac3eb-109">:::no-loc(SignalR)::: tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-109">:::no-loc(SignalR)::: has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ac3eb-110">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="ac3eb-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="ac3eb-111">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ac3eb-112">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `Add:::no-loc(SignalR):::` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-113">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-113">JSON is enabled by default.</span></span> <span data-ttu-id="ac3eb-114">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ac3eb-115">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ac3eb-116">En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ac3eb-117">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ac3eb-118">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="ac3eb-119">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ac3eb-120">Por ejemplo, al `.WithSecurity(MessagePackSecurity.UntrustedData)` reemplazar el `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ac3eb-121">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="ac3eb-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-122">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ac3eb-123">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="ac3eb-124">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ac3eb-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-125">.NET client</span></span>

<span data-ttu-id="ac3eb-126">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-127">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ac3eb-128">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-128">JavaScript client</span></span>

<span data-ttu-id="ac3eb-129">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ac3eb-130">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="ac3eb-131">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ac3eb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ac3eb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="ac3eb-133">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ac3eb-134">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ac3eb-135">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-136">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ac3eb-137">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ac3eb-138">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ac3eb-139">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-140">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="ac3eb-141">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-141">MessagePack quirks</span></span>

<span data-ttu-id="ac3eb-142">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ac3eb-143">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="ac3eb-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ac3eb-144">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ac3eb-145">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ac3eb-146">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ac3eb-147">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ac3eb-148">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ac3eb-149">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ac3eb-150">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ac3eb-151">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="ac3eb-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ac3eb-152">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ac3eb-153">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub se convertirá al formato UTC Si, de `DateTime.Kind` `DateTimeKind.Local` lo contrario, no tocará el tiempo y lo pasará tal cual.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="ac3eb-154">Si está trabajando con `DateTime` valores, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ac3eb-155">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ac3eb-156">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ac3eb-157">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el :::no-loc(SignalR)::: cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ac3eb-158">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ac3eb-159">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ac3eb-160">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ac3eb-161">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ac3eb-162">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ac3eb-163">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ac3eb-164">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-164">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ac3eb-165">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="ac3eb-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ac3eb-166">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ac3eb-167">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ac3eb-168">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ac3eb-169">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="ac3eb-170">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ac3eb-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ac3eb-171">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ac3eb-172">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ac3eb-173">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ac3eb-174">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="ac3eb-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ac3eb-175">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-175">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="ac3eb-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ac3eb-176">Related resources</span></span>

* [<span data-ttu-id="ac3eb-177">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="ac3eb-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ac3eb-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ac3eb-179">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ac3eb-180">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ac3eb-181">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ac3eb-181">What is MessagePack?</span></span>

<span data-ttu-id="ac3eb-182">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ac3eb-183">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ac3eb-184">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ac3eb-185">:::no-loc(SignalR)::: tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-185">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ac3eb-186">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="ac3eb-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="ac3eb-187">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ac3eb-188">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `Add:::no-loc(SignalR):::` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-189">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-189">JSON is enabled by default.</span></span> <span data-ttu-id="ac3eb-190">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ac3eb-191">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ac3eb-192">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ac3eb-193">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ac3eb-194">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="ac3eb-195">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ac3eb-196">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="ac3eb-197">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="ac3eb-198">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-198">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="ac3eb-199">`MessagePack` la versión 2. x presentó cambios importantes y es incompatible con :::no-loc(SignalR)::: las versiones 3,1 y anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with :::no-loc(SignalR)::: versions 3.1 and earlier.</span></span> <span data-ttu-id="ac3eb-200">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="ac3eb-201">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ac3eb-202">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="ac3eb-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-203">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ac3eb-204">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="ac3eb-205">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ac3eb-206">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-206">.NET client</span></span>

<span data-ttu-id="ac3eb-207">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-208">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ac3eb-209">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-209">JavaScript client</span></span>

<span data-ttu-id="ac3eb-210">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ac3eb-211">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="ac3eb-212">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ac3eb-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ac3eb-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="ac3eb-214">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ac3eb-215">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ac3eb-216">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-217">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ac3eb-218">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ac3eb-219">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ac3eb-220">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-221">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="ac3eb-222">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-222">MessagePack quirks</span></span>

<span data-ttu-id="ac3eb-223">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ac3eb-224">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="ac3eb-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ac3eb-225">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ac3eb-226">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ac3eb-227">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ac3eb-228">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ac3eb-229">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ac3eb-230">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ac3eb-231">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ac3eb-232">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="ac3eb-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ac3eb-233">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ac3eb-234">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="ac3eb-235">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ac3eb-236">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="ac3eb-237">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-237">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ac3eb-238">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ac3eb-239">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el :::no-loc(SignalR)::: cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ac3eb-240">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ac3eb-241">El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ac3eb-242">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ac3eb-243">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ac3eb-244">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ac3eb-245">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ac3eb-246">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-246">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ac3eb-247">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="ac3eb-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ac3eb-248">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ac3eb-249">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ac3eb-250">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ac3eb-251">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="ac3eb-252">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ac3eb-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ac3eb-253">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ac3eb-254">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ac3eb-255">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ac3eb-256">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="ac3eb-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ac3eb-257">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-257">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="ac3eb-258">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ac3eb-258">Related resources</span></span>

* [<span data-ttu-id="ac3eb-259">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="ac3eb-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ac3eb-260">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ac3eb-261">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac3eb-262">En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="ac3eb-263">¿Qué es MessagePack?</span><span class="sxs-lookup"><span data-stu-id="ac3eb-263">What is MessagePack?</span></span>

<span data-ttu-id="ac3eb-264">[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="ac3eb-265">Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="ac3eb-266">Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="ac3eb-267">:::no-loc(SignalR)::: tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-267">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="ac3eb-268">Configuración de MessagePack en el servidor</span><span class="sxs-lookup"><span data-stu-id="ac3eb-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="ac3eb-269">Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="ac3eb-270">En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `Add:::no-loc(SignalR):::` llamada para habilitar la compatibilidad con MessagePack en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-271">JSON está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-271">JSON is enabled by default.</span></span> <span data-ttu-id="ac3eb-272">Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="ac3eb-273">Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="ac3eb-274">En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="ac3eb-275">Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="ac3eb-276">Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="ac3eb-277">Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="ac3eb-278">Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="ac3eb-279">La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="ac3eb-280">`MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-280">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="ac3eb-281">Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="ac3eb-282">Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="ac3eb-283">Configuración de MessagePack en el cliente</span><span class="sxs-lookup"><span data-stu-id="ac3eb-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-284">JSON está habilitado de forma predeterminada para los clientes compatibles.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="ac3eb-285">Los clientes solo pueden admitir un único protocolo.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="ac3eb-286">Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="ac3eb-287">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-287">.NET client</span></span>

<span data-ttu-id="ac3eb-288">Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-289">Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="ac3eb-290">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-290">JavaScript client</span></span>

<span data-ttu-id="ac3eb-291">El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="ac3eb-292">Instale el paquete ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="ac3eb-293">Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="ac3eb-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="ac3eb-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="ac3eb-295">En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="ac3eb-296">Use una `<script>` etiqueta para crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="ac3eb-297">La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="ac3eb-298">Al utilizar el `<script>` elemento, el orden es importante.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="ac3eb-299">Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="ac3eb-300">También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="ac3eb-301">Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="ac3eb-302">En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="ac3eb-303">Peculiaridades de MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-303">MessagePack quirks</span></span>

<span data-ttu-id="ac3eb-304">Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="ac3eb-305">MessagePack distingue entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="ac3eb-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="ac3eb-306">El protocolo MessagePack distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="ac3eb-307">Por ejemplo, considere la siguiente clase de C#:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="ac3eb-308">Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="ac3eb-309">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="ac3eb-310">El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="ac3eb-311">Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="ac3eb-312">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="ac3eb-313">DateTime. Kind no se conserva al serializar o deserializar</span><span class="sxs-lookup"><span data-stu-id="ac3eb-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="ac3eb-314">El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ac3eb-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="ac3eb-315">Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="ac3eb-316">Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="ac3eb-317">Conviértalos de la hora UTC a la hora local cuando los reciba.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="ac3eb-318">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-318">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="ac3eb-319">No se admite DateTime. MinValue en MessagePack en JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="ac3eb-320">La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el :::no-loc(SignalR)::: cliente JavaScript no admite el `timestamp96` tipo en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="ac3eb-321">Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="ac3eb-322">El valor de `DateTime.MinValue` es `January 1, 0001` que se debe codificar en un `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="ac3eb-323">Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="ac3eb-324">Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="ac3eb-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="ac3eb-325">Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="ac3eb-326">Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="ac3eb-327">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-327">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="ac3eb-328">Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"</span><span class="sxs-lookup"><span data-stu-id="ac3eb-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="ac3eb-329">La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="ac3eb-330">Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="ac3eb-331">Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="ac3eb-332">Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="ac3eb-333">Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="ac3eb-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="ac3eb-334">Las comprobaciones de tipo son más estrictas en MessagePack</span><span class="sxs-lookup"><span data-stu-id="ac3eb-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="ac3eb-335">El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="ac3eb-336">Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá.</span><span class="sxs-lookup"><span data-stu-id="ac3eb-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="ac3eb-337">Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):</span><span class="sxs-lookup"><span data-stu-id="ac3eb-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="ac3eb-338">Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="ac3eb-338">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="ac3eb-339">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="ac3eb-339">Related resources</span></span>

* [<span data-ttu-id="ac3eb-340">Primeros pasos</span><span class="sxs-lookup"><span data-stu-id="ac3eb-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ac3eb-341">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ac3eb-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ac3eb-342">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac3eb-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
