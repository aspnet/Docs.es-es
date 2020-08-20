---
title: Detalles de cifrado autenticado en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la implementación de ASP.NET Core el cifrado autenticado de protección de datos.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ed75ab235a95a88bbe60615526137b4c2bb719ef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630853"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Detalles de cifrado autenticado en ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Las llamadas a IDataProtector. Protect son operaciones de cifrado autenticadas. El método Protect ofrece confidencialidad y autenticidad, y está asociado a la cadena de propósito que se usó para derivar esta instancia concreta de IDataProtector de su IDataProtectionProvider raíz.

IDataProtector. Protect toma un parámetro de texto simple Byte [] y genera una carga protegida de Byte [], cuyo formato se describe a continuación. (También hay una sobrecarga del método de extensión que toma un parámetro de texto simple de cadena y devuelve una carga protegida de cadena. Si se usa esta API, el formato de carga protegida seguirá teniendo la siguiente estructura, pero se [codificará con base64url](https://tools.ietf.org/html/rfc4648#section-5)).

## <a name="protected-payload-format"></a>Formato de carga protegida

El formato de carga protegido consta de tres componentes principales:

* Un encabezado mágico de 32 bits que identifica la versión del sistema de protección de datos.

* Identificador de clave de 128 bits que identifica la clave utilizada para proteger esta carga concreta.

* El resto de la carga protegida es [específico del sistema de cifrado encapsulado por esta clave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). En el ejemplo siguiente, la clave representa un sistema de cifrado AES-256-CBC + HMACSHA256 y la carga se subdivide más como sigue:
  * Modificador de clave de 128 bits.
  * Un vector de inicialización de 128 bits.
  * 48 bytes de salida AES-256-CBC.
  * Una etiqueta de autenticación HMACSHA256.

A continuación se muestra una carga protegida de ejemplo.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Desde el formato de carga superior a los primeros 32 bits, o 4 bytes son el encabezado mágico que identifica la versión (09 F0 C9 F0)

Los siguientes 128 bits, o 16 bytes, es el identificador de clave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

El resto contiene la carga útil y es específico del formato utilizado.

> [!WARNING]
> Todas las cargas protegidas en una clave determinada comenzarán con el mismo encabezado de 20 bytes (valor mágico, ID. de clave). Los administradores pueden usar este hecho con fines de diagnóstico aproximados cuando se genera una carga. Por ejemplo, la carga anterior corresponde a la clave {0c819c80-6619-4019-9536-53f8aaffee57}. Si después de comprobar el repositorio de claves detecta que la fecha de activación de esta clave específica era 2015-01-01 y su fecha de expiración era 2015-03-01, es razonable asumir que la carga (si no se ha manipulado) se generó dentro de esa ventana, dar o tomar un pequeño factor de Fudge en cada lado.
