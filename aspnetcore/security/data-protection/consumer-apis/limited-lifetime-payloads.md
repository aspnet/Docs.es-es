---
title: Limitar la duración de las cargas protegidas en ASP.NET Core
author: rick-anderson
description: Obtenga información acerca de cómo limitar la duración de una carga protegida mediante las API de protección de datos de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060799"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="72e9c-103">Limitar la duración de las cargas protegidas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72e9c-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="72e9c-104">Hay escenarios en los que el desarrollador de aplicaciones desea crear una carga protegida que expire después de un período de tiempo establecido.</span><span class="sxs-lookup"><span data-stu-id="72e9c-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="72e9c-105">Por ejemplo, la carga protegida podría representar un token de restablecimiento de contraseña que solo debe ser válido durante una hora.</span><span class="sxs-lookup"><span data-stu-id="72e9c-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="72e9c-106">Es posible que el desarrollador cree su propio formato de carga útil que contenga una fecha de expiración incrustada y que los desarrolladores avanzados quieran hacerlo de todos modos, pero para la mayoría de los desarrolladores que administran estas expiraciones pueden resultar tediosos.</span><span class="sxs-lookup"><span data-stu-id="72e9c-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="72e9c-107">Para facilitar esta tarea a los desarrolladores, el paquete [Microsoft. AspNetCore. deprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene las API de la utilidad para crear cargas que expiran automáticamente después de un período de tiempo establecido.</span><span class="sxs-lookup"><span data-stu-id="72e9c-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="72e9c-108">Estas API no responden del `ITimeLimitedDataProtector` tipo.</span><span class="sxs-lookup"><span data-stu-id="72e9c-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="72e9c-109">Uso de API</span><span class="sxs-lookup"><span data-stu-id="72e9c-109">API usage</span></span>

<span data-ttu-id="72e9c-110">La `ITimeLimitedDataProtector` interfaz es la interfaz principal para proteger y desproteger las cargas de tiempo limitado o de expiración automática.</span><span class="sxs-lookup"><span data-stu-id="72e9c-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="72e9c-111">Para crear una instancia de `ITimeLimitedDataProtector` , primero necesitará una instancia de un [IDataProtector](xref:security/data-protection/consumer-apis/overview) regular construido con un propósito específico.</span><span class="sxs-lookup"><span data-stu-id="72e9c-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="72e9c-112">Una vez que la `IDataProtector` instancia esté disponible, llame al `IDataProtector.ToTimeLimitedDataProtector` método de extensión para obtener un protector con capacidades de expiración integradas.</span><span class="sxs-lookup"><span data-stu-id="72e9c-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="72e9c-113">`ITimeLimitedDataProtector` expone los siguientes métodos de extensión y superficie de API:</span><span class="sxs-lookup"><span data-stu-id="72e9c-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="72e9c-114">CreateProtector (propósito de cadena): ITimeLimitedDataProtector: esta API es similar a la existente, ya `IDataProtectionProvider.CreateProtector` que se puede usar para crear [cadenas de propósito](xref:security/data-protection/consumer-apis/purpose-strings) a partir de un protector de hora raíz limitado.</span><span class="sxs-lookup"><span data-stu-id="72e9c-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="72e9c-115">Proteger (Byte [] texto simple, expiración de DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="72e9c-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="72e9c-116">Proteger (Byte [] texto simple, duración de TimeSpan): Byte []</span><span class="sxs-lookup"><span data-stu-id="72e9c-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="72e9c-117">Proteger (Byte [] texto simple): Byte []</span><span class="sxs-lookup"><span data-stu-id="72e9c-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="72e9c-118">Protect (cadena sin formato, expiración de DateTimeOffset): cadena</span><span class="sxs-lookup"><span data-stu-id="72e9c-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="72e9c-119">Protect (cadena sin formato, duración de TimeSpan): cadena</span><span class="sxs-lookup"><span data-stu-id="72e9c-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="72e9c-120">Protect (cadena sin formato): cadena</span><span class="sxs-lookup"><span data-stu-id="72e9c-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="72e9c-121">Además de los métodos básicos `Protect` que toman solo el texto simple, hay nuevas sobrecargas que permiten especificar la fecha de expiración de la carga.</span><span class="sxs-lookup"><span data-stu-id="72e9c-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="72e9c-122">La fecha de expiración se puede especificar como una fecha absoluta (a través de `DateTimeOffset` ) o como un tiempo relativo (de la hora del sistema actual, a través de `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="72e9c-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="72e9c-123">Si se llama a una sobrecarga que no toma una expiración, se supone que la carga no expira nunca.</span><span class="sxs-lookup"><span data-stu-id="72e9c-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="72e9c-124">Unprotect (Byte [] protectedData, out DateTimeOffset de salida): Byte []</span><span class="sxs-lookup"><span data-stu-id="72e9c-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="72e9c-125">Unprotect (Byte [] protectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="72e9c-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="72e9c-126">Unprotect (cadena protectedData, out DateTimeOffset expire): cadena</span><span class="sxs-lookup"><span data-stu-id="72e9c-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="72e9c-127">Unprotect (cadena protectedData): cadena</span><span class="sxs-lookup"><span data-stu-id="72e9c-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="72e9c-128">Los `Unprotect` métodos devuelven los datos no protegidos originales.</span><span class="sxs-lookup"><span data-stu-id="72e9c-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="72e9c-129">Si la carga aún no ha expirado, la expiración absoluta se devuelve como un parámetro out opcional junto con los datos no protegidos originales.</span><span class="sxs-lookup"><span data-stu-id="72e9c-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="72e9c-130">Si la carga ha expirado, todas las sobrecargas del método Unprotect producirán CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="72e9c-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="72e9c-131">No se recomienda usar estas API para proteger las cargas que requieren una persistencia a largo plazo o indefinida.</span><span class="sxs-lookup"><span data-stu-id="72e9c-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="72e9c-132">"¿Puedo permitirse que las cargas protegidas sean permanentemente irrecuperables después de un mes?"</span><span class="sxs-lookup"><span data-stu-id="72e9c-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="72e9c-133">puede servir como una buena regla general; Si la respuesta es no, los desarrolladores deben considerar API alternativas.</span><span class="sxs-lookup"><span data-stu-id="72e9c-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="72e9c-134">En el ejemplo siguiente se usan las rutas de acceso de [código no di](xref:security/data-protection/configuration/non-di-scenarios) para crear instancias del sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="72e9c-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="72e9c-135">Para ejecutar este ejemplo, asegúrese de que ha agregado primero una referencia al paquete Microsoft. AspNetCore. desproteccion. Extensions.</span><span class="sxs-lookup"><span data-stu-id="72e9c-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
