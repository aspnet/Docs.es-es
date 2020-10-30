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
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058173"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>Usar el protocolo MessagePack Hub en SignalR para ASP.net Core

::: moniker range=">= aspnetcore-5.0"

En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto. Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/). Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack. SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.

## <a name="configure-messagepack-on-the-server"></a>Configuración de MessagePack en el servidor

Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación. En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones. En ese delegado, la `SerializerOptions` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.

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
> Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas. Por ejemplo, al `.WithSecurity(MessagePackSecurity.UntrustedData)` reemplazar el `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Configuración de MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes compatibles. Los clientes solo pueden admitir un único protocolo. Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) . Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca. Use una `<script>` etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .

> [!NOTE]
> Al utilizar el `<script>` elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack. También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades de MessagePack

Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue entre mayúsculas y minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#. Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind no se conserva al serializar o deserializar

El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` . Como resultado, al deserializar una fecha, el protocolo MessagePack Hub se convertirá al formato UTC Si, de `DateTime.Kind` `DateTimeKind.Local` lo contrario, no tocará el tiempo y lo pasará tal cual. Si está trabajando con `DateTime` valores, se recomienda convertir a UTC antes de enviarlos. Conviértalos de la hora UTC a la hora local cuando los reciba.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>No se admite DateTime. MinValue en MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack. Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro). El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor. Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value. Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity). Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto. Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/). Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack. SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.

## <a name="configure-messagepack-on-the-server"></a>Configuración de MessagePack en el servidor

Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación. En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones. En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.

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
> Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas. Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` . La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR . `MessagePack` la versión 2. x presentó cambios importantes y es incompatible con SignalR las versiones 3,1 y anteriores. Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio. Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configuración de MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes compatibles. Los clientes solo pueden admitir un único protocolo. Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) . Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca. Use una `<script>` etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .

> [!NOTE]
> Al utilizar el `<script>` elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack. También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades de MessagePack

Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue entre mayúsculas y minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#. Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind no se conserva al serializar o deserializar

El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` . Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC. Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos. Conviértalos de la hora UTC a la hora local cuando los reciba.

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>No se admite DateTime. MinValue en MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack. Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro). El valor de `DateTime.MinValue` es `January 1, 0001` , que se debe codificar en un `timestamp96` valor. Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value. Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity). Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se [da por supuesto](xref:tutorials/signalr)que el lector está familiarizado con los temas descritos en introducción.

## <a name="what-is-messagepack"></a>¿Qué es MessagePack?

[MessagePack](https://msgpack.org/index.html) es un formato de serialización binario rápido y compacto. Resulta útil cuando el rendimiento y el ancho de banda son un problema porque crea mensajes más pequeños en comparación con [JSON](https://www.json.org/). Los mensajes binarios son ilegibles al examinar los seguimientos de red y los registros a menos que los bytes se pasen a través de un analizador de MessagePack. SignalR tiene compatibilidad integrada con el formato MessagePack y proporciona las API para que las use el cliente y el servidor.

## <a name="configure-messagepack-on-the-server"></a>Configuración de MessagePack en el servidor

Para habilitar el protocolo MessagePack Hub en el servidor, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete en la aplicación. En el `Startup.ConfigureServices` método, agregue `AddMessagePackProtocol` a la `AddSignalR` llamada para habilitar la compatibilidad con MessagePack en el servidor.

> [!NOTE]
> JSON está habilitado de forma predeterminada. Agregar MessagePack habilita la compatibilidad con los clientes JSON y MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar la forma en que MessagePack dará formato a los datos, `AddMessagePackProtocol` toma un delegado para configurar las opciones. En ese delegado, la `FormatterResolvers` propiedad se puede usar para configurar las opciones de serialización de MessagePack. Para obtener más información sobre cómo funcionan los solucionadores, visite la biblioteca MessagePack en [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Los atributos se pueden usar en los objetos que se van a serializar para definir cómo deben administrarse.

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
> Se recomienda encarecidamente revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) y aplicar las revisiones recomendadas. Por ejemplo, establecer la `MessagePackSecurity.Active` propiedad estática en `MessagePackSecurity.UntrustedData` . La configuración de `MessagePackSecurity.Active` requiere la instalación manual [de una versión 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`La instalación de las actualizaciones de 1.9. x utiliza la versión SignalR . Cuando `MessagePackSecurity.Active` no se establece en `MessagePackSecurity.UntrustedData` , un cliente malintencionado podría producir una denegación de servicio. Establezca `MessagePackSecurity.Active` en `Program.Main` , como se muestra en el código siguiente:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configuración de MessagePack en el cliente

> [!NOTE]
> JSON está habilitado de forma predeterminada para los clientes compatibles. Los clientes solo pueden admitir un único protocolo. Al agregar compatibilidad con MessagePack se reemplazarán todos los protocolos configurados previamente.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack en el cliente de .NET, instale el `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paquete y llame a `AddMessagePackProtocol` en `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Esta `AddMessagePackProtocol` llamada toma un delegado para configurar opciones como el servidor.

### <a name="javascript-client"></a>Cliente de JavaScript

El paquete NPM proporciona compatibilidad con MessagePack para el cliente de JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) . Instale el paquete ejecutando el siguiente comando en un shell de comandos:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Después de instalar el paquete NPM, el módulo se puede usar directamente a través de un cargador de módulos de JavaScript o importarse en el explorador haciendo referencia al archivo siguiente:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

En un explorador, `msgpack5` también se debe hacer referencia a la biblioteca. Use una `<script>` etiqueta para crear una referencia. La biblioteca se puede encontrar en *node_modules\msgpack5\dist\msgpack5.js* .

> [!NOTE]
> Al utilizar el `<script>` elemento, el orden es importante. Si se hace referencia a *signalr-protocol-msgpack.js* antes de *msgpack5.js* , se produce un error al intentar conectarse con MessagePack. También se requiere *signalr.js* antes de *signalr-protocol-msgpack.js* .

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Al agregar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` a `HubConnectionBuilder` , se configurará el cliente para que use el protocolo MessagePack al conectarse a un servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> En este momento, no hay ninguna opción de configuración para el protocolo MessagePack en el cliente de JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades de MessagePack

Hay algunos problemas que se deben tener en cuenta al usar el protocolo MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack distingue entre mayúsculas y minúsculas

El protocolo MessagePack distingue mayúsculas de minúsculas. Por ejemplo, considere la siguiente clase de C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Cuando se envía desde el cliente de JavaScript, se deben usar `PascalCased` nombres de propiedad, ya que las mayúsculas y minúsculas deben coincidir exactamente con la clase de C#. Por ejemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

El uso de `camelCased` nombres no se enlaza correctamente a la clase de C#. Puede solucionar este paso mediante el `Key` atributo para especificar un nombre diferente para la propiedad MessagePack. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind no se conserva al serializar o deserializar

El protocolo MessagePack no proporciona una manera de codificar el `Kind` valor de un `DateTime` . Como resultado, al deserializar una fecha, el protocolo MessagePack Hub supone que la fecha de entrada está en formato UTC. Si está trabajando con `DateTime` valores en la hora local, se recomienda convertir a UTC antes de enviarlos. Conviértalos de la hora UTC a la hora local cuando los reciba.

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>No se admite DateTime. MinValue en MessagePack en JavaScript

La biblioteca [msgpack5](https://github.com/mcollina/msgpack5) que usa el SignalR cliente JavaScript no admite el `timestamp96` tipo en MessagePack. Este tipo se usa para codificar valores de fecha muy grandes (ya sea muy temprano en el pasado o muy lejos en el futuro). El valor de `DateTime.MinValue` es `January 1, 0001` que se debe codificar en un `timestamp96` valor. Debido a esto, `DateTime.MinValue` no se admite el envío a un cliente de JavaScript. Cuando `DateTime.MinValue` el cliente JavaScript recibe el mensaje, se produce el siguiente error:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Normalmente, `DateTime.MinValue` se usa para codificar "Missing" o `null` Value. Si necesita codificar ese valor en MessagePack, use un valor que acepte valores NULL `DateTime` ( `DateTime?` ) o codifique un valor independiente `bool` que indique si la fecha está presente.

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Compatibilidad con MessagePack en el entorno de compilación "anterior a la hora"

La biblioteca [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizada por el cliente y el servidor .net utiliza la generación de código para optimizar la serialización. Como resultado, no se admite de forma predeterminada en los entornos que usan la compilación "anterior a la hora" (como Xamarin iOS o Unity). Es posible usar MessagePack en estos entornos mediante la "generación previa" del código del serializador/deserializador. Para obtener más información, consulte [la documentación de MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una vez que haya generado los serializadores previamente, puede registrarlos mediante el delegado de configuración que se pasa a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Las comprobaciones de tipo son más estrictas en MessagePack

El protocolo del concentrador de JSON realizará las conversiones de tipos durante la deserialización. Por ejemplo, si el objeto entrante tiene un valor de propiedad que es un número ( `{ foo: 42 }` ) pero la propiedad de la clase .net es de tipo `string` , el valor se convertirá. Sin embargo, MessagePack no realiza esta conversión y producirá una excepción que se puede observar en los registros del lado servidor (y en la consola):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obtener más información sobre esta limitación, vea el tema sobre el problema de GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Primeros pasos](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)

::: moniker-end
