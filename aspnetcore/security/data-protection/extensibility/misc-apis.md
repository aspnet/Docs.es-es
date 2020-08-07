---
title: Varias API de protección de datos ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la ASP.NET Core interfaz de ISecret de protección de datos.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913804"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="487ee-103">Varias API de protección de datos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="487ee-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="487ee-104">Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.</span><span class="sxs-lookup"><span data-stu-id="487ee-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="487ee-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="487ee-105">ISecret</span></span>

<span data-ttu-id="487ee-106">La `ISecret` interfaz representa un valor secreto, como material de clave criptográfica.</span><span class="sxs-lookup"><span data-stu-id="487ee-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="487ee-107">Contiene la siguiente superficie de API:</span><span class="sxs-lookup"><span data-stu-id="487ee-107">It contains the following API surface:</span></span>

* <span data-ttu-id="487ee-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="487ee-108">`Length`: `int`</span></span>

* <span data-ttu-id="487ee-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="487ee-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="487ee-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="487ee-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="487ee-111">El `WriteSecretIntoBuffer` método rellena el búfer proporcionado con el valor de secreto sin formato.</span><span class="sxs-lookup"><span data-stu-id="487ee-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="487ee-112">La razón por la que esta API toma el búfer como parámetro en lugar de devolver un elemento `byte[]` directamente es que permite al llamador la oportunidad de anclar el objeto de búfer, lo que limita la exposición secreta al recolector de elementos no utilizados administrado.</span><span class="sxs-lookup"><span data-stu-id="487ee-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="487ee-113">El `Secret` tipo es una implementación concreta de `ISecret` donde el valor secreto se almacena en memoria en proceso.</span><span class="sxs-lookup"><span data-stu-id="487ee-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="487ee-114">En las plataformas de Windows, el valor secreto se cifra mediante [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="487ee-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
