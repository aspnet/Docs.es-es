---
title: SignalR Consideraciones sobre el diseño de API
author: anurse
description: Obtenga información sobre cómo diseñar SignalR API para la compatibilidad entre versiones de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059655"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR Consideraciones sobre el diseño de API

Por [Andrew Stanton-enfermera](https://twitter.com/anurse)

En este artículo se proporcionan instrucciones para crear SignalR API basadas en.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Usar parámetros de objeto personalizados para garantizar la compatibilidad con versiones anteriores

Agregar parámetros a un SignalR método de concentrador (tanto en el cliente como en el servidor) es un *cambio importante* . Esto significa que los clientes o servidores más antiguos obtendrán errores al intentar invocar el método sin el número adecuado de parámetros. Sin embargo, agregar propiedades a un parámetro de objeto personalizado **no** es un cambio importante. Se puede usar para diseñar API compatibles que sean resistentes a los cambios en el cliente o el servidor.

Por ejemplo, considere una API del lado servidor como la siguiente:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

El cliente JavaScript llama a este método mediante `invoke` de la manera siguiente:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Si posteriormente agrega un segundo parámetro al método de servidor, los clientes más antiguos no proporcionarán este valor de parámetro. Por ejemplo:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Cuando el cliente anterior intenta invocar este método, obtendrá un error similar al siguiente:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

En el servidor, verá un mensaje de registro similar al siguiente:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

El cliente anterior solo envió un parámetro, pero la API de servidor más reciente necesitaba dos parámetros. El uso de objetos personalizados como parámetros proporciona más flexibilidad. Vamos a rediseñar la API original para usar un objeto personalizado:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Ahora, el cliente usa un objeto para llamar al método:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

En lugar de agregar un parámetro, agregue una propiedad al `TotalLengthRequest` objeto:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Cuando el cliente anterior envía un parámetro único, se `Param2` deja la propiedad adicional `null` . Puede detectar un mensaje enviado por un cliente anterior si comprueba `Param2` `null` y aplica un valor predeterminado. Un nuevo cliente puede enviar ambos parámetros.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

La misma técnica funciona en el caso de los métodos definidos en el cliente. Puede enviar un objeto personalizado desde el lado servidor:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

En el lado cliente, tiene acceso a la `Message` propiedad en lugar de usar un parámetro:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Si más adelante decide agregar el remitente del mensaje a la carga, agregue una propiedad al objeto:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Los clientes más antiguos no esperan el `Sender` valor, por lo que lo omitirán. Un nuevo cliente puede aceptarlo actualizando para leer la nueva propiedad:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

En este caso, el nuevo cliente también es tolerante a un servidor antiguo que no proporciona el `Sender` valor. Dado que el servidor anterior no proporcionará el `Sender` valor, el cliente comprueba si existe antes de tener acceso a él.
