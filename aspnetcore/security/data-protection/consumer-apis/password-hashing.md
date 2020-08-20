---
title: Contraseñas de hash en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo aplicar un algoritmo hash a las contraseñas mediante las API de protección de datos de ASP.NET Core.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 19263400397a9dfe2d9e6044109d6d063023f6f4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629865"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="3910a-103">Contraseñas de hash en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3910a-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="3910a-104">La base de código de protección de datos incluye un paquete *Microsoft. AspNetCore. Cryptography. derivación* que contiene las funciones de derivación de claves de cifrado.</span><span class="sxs-lookup"><span data-stu-id="3910a-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="3910a-105">Este paquete es un componente independiente y no tiene dependencias en el resto del sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="3910a-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="3910a-106">Se puede usar de forma totalmente independiente.</span><span class="sxs-lookup"><span data-stu-id="3910a-106">It can be used completely independently.</span></span> <span data-ttu-id="3910a-107">El origen existe junto con el código base de protección de datos como una comodidad.</span><span class="sxs-lookup"><span data-stu-id="3910a-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="3910a-108">El paquete ofrece actualmente un método `KeyDerivation.Pbkdf2` que permite aplicar un algoritmo hash a una contraseña mediante el [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="3910a-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="3910a-109">Esta API es muy similar al [tipo Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente del .NET Framework, pero hay tres diferencias importantes:</span><span class="sxs-lookup"><span data-stu-id="3910a-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="3910a-110">El `KeyDerivation.Pbkdf2` método admite el consumo de varias PRFS (actualmente `HMACSHA1` , `HMACSHA256` y `HMACSHA512` ), mientras que el `Rfc2898DeriveBytes` tipo solo admite `HMACSHA1` .</span><span class="sxs-lookup"><span data-stu-id="3910a-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="3910a-111">El `KeyDerivation.Pbkdf2` método detecta el sistema operativo actual e intenta elegir la implementación más optimizada de la rutina, lo que proporciona un rendimiento mucho mejor en ciertos casos.</span><span class="sxs-lookup"><span data-stu-id="3910a-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="3910a-112">(En Windows 8, ofrece aproximadamente 10 veces el rendimiento de `Rfc2898DeriveBytes` ).</span><span class="sxs-lookup"><span data-stu-id="3910a-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="3910a-113">El `KeyDerivation.Pbkdf2` método requiere que el llamador especifique todos los parámetros (sal, PRF y recuento de iteraciones).</span><span class="sxs-lookup"><span data-stu-id="3910a-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="3910a-114">El `Rfc2898DeriveBytes` tipo proporciona los valores predeterminados para estos.</span><span class="sxs-lookup"><span data-stu-id="3910a-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="3910a-115">Vea el [código fuente](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity del `PasswordHasher` tipo de un caso de uso real.</span><span class="sxs-lookup"><span data-stu-id="3910a-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
