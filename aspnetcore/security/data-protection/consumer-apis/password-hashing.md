---
title: Contraseñas de hash en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo aplicar un algoritmo hash a las contraseñas mediante las API de protección de datos de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051868"
---
# <a name="hash-passwords-in-aspnet-core"></a>Contraseñas de hash en ASP.NET Core

La base de código de protección de datos incluye un paquete *Microsoft. AspNetCore. Cryptography. derivación* que contiene las funciones de derivación de claves de cifrado. Este paquete es un componente independiente y no tiene dependencias en el resto del sistema de protección de datos. Se puede usar de forma totalmente independiente. El origen existe junto con el código base de protección de datos como una comodidad.

El paquete ofrece actualmente un método `KeyDerivation.Pbkdf2` que permite aplicar un algoritmo hash a una contraseña mediante el [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Esta API es muy similar al [tipo Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente del .NET Framework, pero hay tres diferencias importantes:

1. El `KeyDerivation.Pbkdf2` método admite el consumo de varias PRFS (actualmente `HMACSHA1` , `HMACSHA256` y `HMACSHA512` ), mientras que el `Rfc2898DeriveBytes` tipo solo admite `HMACSHA1` .

2. El `KeyDerivation.Pbkdf2` método detecta el sistema operativo actual e intenta elegir la implementación más optimizada de la rutina, lo que proporciona un rendimiento mucho mejor en ciertos casos. (En Windows 8, ofrece aproximadamente 10 veces el rendimiento de `Rfc2898DeriveBytes` ).

3. El `KeyDerivation.Pbkdf2` método requiere que el llamador especifique todos los parámetros (sal, PRF y recuento de iteraciones). El `Rfc2898DeriveBytes` tipo proporciona los valores predeterminados para estos.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Vea el [código fuente](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity del `PasswordHasher` tipo de un caso de uso real.
