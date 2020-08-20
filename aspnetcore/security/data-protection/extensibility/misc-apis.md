---
title: Varias API de protección de datos ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la ASP.NET Core interfaz de ISecret de protección de datos.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630918"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Varias API de protección de datos ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.

## <a name="isecret"></a>ISecret

La `ISecret` interfaz representa un valor secreto, como material de clave criptográfica. Contiene la siguiente superficie de API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

El `WriteSecretIntoBuffer` método rellena el búfer proporcionado con el valor de secreto sin formato. La razón por la que esta API toma el búfer como parámetro en lugar de devolver un elemento `byte[]` directamente es que permite al llamador la oportunidad de anclar el objeto de búfer, lo que limita la exposición secreta al recolector de elementos no utilizados administrado.

El `Secret` tipo es una implementación concreta de `ISecret` donde el valor secreto se almacena en memoria en proceso. En las plataformas de Windows, el valor secreto se cifra mediante [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
