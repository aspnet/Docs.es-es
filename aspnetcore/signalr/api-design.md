---
title: 'SignalR Consideraciones sobre el diseño de API'
author: anurse
description: 'Obtenga información sobre cómo diseñar SignalR API para la compatibilidad entre versiones de la aplicación.'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059655"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="1410d-103">SignalR Consideraciones sobre el diseño de API</span><span class="sxs-lookup"><span data-stu-id="1410d-103">SignalR API design considerations</span></span>

<span data-ttu-id="1410d-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="1410d-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="1410d-105">En este artículo se proporcionan instrucciones para crear SignalR API basadas en.</span><span class="sxs-lookup"><span data-stu-id="1410d-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="1410d-106">Usar parámetros de objeto personalizados para garantizar la compatibilidad con versiones anteriores</span><span class="sxs-lookup"><span data-stu-id="1410d-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="1410d-107">Agregar parámetros a un SignalR método de concentrador (tanto en el cliente como en el servidor) es un *cambio importante* .</span><span class="sxs-lookup"><span data-stu-id="1410d-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change* .</span></span> <span data-ttu-id="1410d-108">Esto significa que los clientes o servidores más antiguos obtendrán errores al intentar invocar el método sin el número adecuado de parámetros.</span><span class="sxs-lookup"><span data-stu-id="1410d-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="1410d-109">Sin embargo, agregar propiedades a un parámetro de objeto personalizado **no** es un cambio importante.</span><span class="sxs-lookup"><span data-stu-id="1410d-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="1410d-110">Se puede usar para diseñar API compatibles que sean resistentes a los cambios en el cliente o el servidor.</span><span class="sxs-lookup"><span data-stu-id="1410d-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="1410d-111">Por ejemplo, considere una API del lado servidor como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="1410d-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="1410d-112">El cliente JavaScript llama a este método mediante `invoke` de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="1410d-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="1410d-113">Si posteriormente agrega un segundo parámetro al método de servidor, los clientes más antiguos no proporcionarán este valor de parámetro.</span><span class="sxs-lookup"><span data-stu-id="1410d-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="1410d-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1410d-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="1410d-115">Cuando el cliente anterior intenta invocar este método, obtendrá un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="1410d-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="1410d-116">En el servidor, verá un mensaje de registro similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="1410d-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="1410d-117">El cliente anterior solo envió un parámetro, pero la API de servidor más reciente necesitaba dos parámetros.</span><span class="sxs-lookup"><span data-stu-id="1410d-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="1410d-118">El uso de objetos personalizados como parámetros proporciona más flexibilidad.</span><span class="sxs-lookup"><span data-stu-id="1410d-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="1410d-119">Vamos a rediseñar la API original para usar un objeto personalizado:</span><span class="sxs-lookup"><span data-stu-id="1410d-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="1410d-120">Ahora, el cliente usa un objeto para llamar al método:</span><span class="sxs-lookup"><span data-stu-id="1410d-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="1410d-121">En lugar de agregar un parámetro, agregue una propiedad al `TotalLengthRequest` objeto:</span><span class="sxs-lookup"><span data-stu-id="1410d-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="1410d-122">Cuando el cliente anterior envía un parámetro único, se `Param2` deja la propiedad adicional `null` .</span><span class="sxs-lookup"><span data-stu-id="1410d-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="1410d-123">Puede detectar un mensaje enviado por un cliente anterior si comprueba `Param2` `null` y aplica un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1410d-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="1410d-124">Un nuevo cliente puede enviar ambos parámetros.</span><span class="sxs-lookup"><span data-stu-id="1410d-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="1410d-125">La misma técnica funciona en el caso de los métodos definidos en el cliente.</span><span class="sxs-lookup"><span data-stu-id="1410d-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="1410d-126">Puede enviar un objeto personalizado desde el lado servidor:</span><span class="sxs-lookup"><span data-stu-id="1410d-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="1410d-127">En el lado cliente, tiene acceso a la `Message` propiedad en lugar de usar un parámetro:</span><span class="sxs-lookup"><span data-stu-id="1410d-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="1410d-128">Si más adelante decide agregar el remitente del mensaje a la carga, agregue una propiedad al objeto:</span><span class="sxs-lookup"><span data-stu-id="1410d-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="1410d-129">Los clientes más antiguos no esperan el `Sender` valor, por lo que lo omitirán.</span><span class="sxs-lookup"><span data-stu-id="1410d-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="1410d-130">Un nuevo cliente puede aceptarlo actualizando para leer la nueva propiedad:</span><span class="sxs-lookup"><span data-stu-id="1410d-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="1410d-131">En este caso, el nuevo cliente también es tolerante a un servidor antiguo que no proporciona el `Sender` valor.</span><span class="sxs-lookup"><span data-stu-id="1410d-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="1410d-132">Dado que el servidor anterior no proporcionará el `Sender` valor, el cliente comprueba si existe antes de tener acceso a él.</span><span class="sxs-lookup"><span data-stu-id="1410d-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
