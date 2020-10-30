---
title: Detalles de cifrado autenticado en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la implementación de ASP.NET Core el cifrado autenticado de protección de datos.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051842"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="9ef98-103">Detalles de cifrado autenticado en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ef98-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="9ef98-104">Las llamadas a IDataProtector. Protect son operaciones de cifrado autenticadas.</span><span class="sxs-lookup"><span data-stu-id="9ef98-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="9ef98-105">El método Protect ofrece confidencialidad y autenticidad, y está asociado a la cadena de propósito que se usó para derivar esta instancia concreta de IDataProtector de su IDataProtectionProvider raíz.</span><span class="sxs-lookup"><span data-stu-id="9ef98-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="9ef98-106">IDataProtector. Protect toma un parámetro de texto simple Byte [] y genera una carga protegida de Byte [], cuyo formato se describe a continuación.</span><span class="sxs-lookup"><span data-stu-id="9ef98-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="9ef98-107">(También hay una sobrecarga del método de extensión que toma un parámetro de texto simple de cadena y devuelve una carga protegida de cadena.</span><span class="sxs-lookup"><span data-stu-id="9ef98-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="9ef98-108">Si se usa esta API, el formato de carga protegida seguirá teniendo la siguiente estructura, pero se [codificará con base64url](https://tools.ietf.org/html/rfc4648#section-5)).</span><span class="sxs-lookup"><span data-stu-id="9ef98-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="9ef98-109">Formato de carga protegida</span><span class="sxs-lookup"><span data-stu-id="9ef98-109">Protected payload format</span></span>

<span data-ttu-id="9ef98-110">El formato de carga protegido consta de tres componentes principales:</span><span class="sxs-lookup"><span data-stu-id="9ef98-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="9ef98-111">Un encabezado mágico de 32 bits que identifica la versión del sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="9ef98-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="9ef98-112">Identificador de clave de 128 bits que identifica la clave utilizada para proteger esta carga concreta.</span><span class="sxs-lookup"><span data-stu-id="9ef98-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="9ef98-113">El resto de la carga protegida es [específico del sistema de cifrado encapsulado por esta clave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="9ef98-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="9ef98-114">En el ejemplo siguiente, la clave representa un sistema de cifrado AES-256-CBC + HMACSHA256 y la carga se subdivide más como sigue:</span><span class="sxs-lookup"><span data-stu-id="9ef98-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="9ef98-115">Modificador de clave de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="9ef98-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="9ef98-116">Un vector de inicialización de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="9ef98-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="9ef98-117">48 bytes de salida AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="9ef98-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="9ef98-118">Una etiqueta de autenticación HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="9ef98-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="9ef98-119">A continuación se muestra una carga protegida de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="9ef98-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="9ef98-120">Desde el formato de carga superior a los primeros 32 bits, o 4 bytes son el encabezado mágico que identifica la versión (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="9ef98-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="9ef98-121">Los siguientes 128 bits, o 16 bytes, es el identificador de clave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="9ef98-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="9ef98-122">El resto contiene la carga útil y es específico del formato utilizado.</span><span class="sxs-lookup"><span data-stu-id="9ef98-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="9ef98-123">Todas las cargas protegidas en una clave determinada comenzarán con el mismo encabezado de 20 bytes (valor mágico, ID. de clave).</span><span class="sxs-lookup"><span data-stu-id="9ef98-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="9ef98-124">Los administradores pueden usar este hecho con fines de diagnóstico aproximados cuando se genera una carga.</span><span class="sxs-lookup"><span data-stu-id="9ef98-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="9ef98-125">Por ejemplo, la carga anterior corresponde a la clave {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="9ef98-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="9ef98-126">Si después de comprobar el repositorio de claves detecta que la fecha de activación de esta clave específica era 2015-01-01 y su fecha de expiración era 2015-03-01, es razonable asumir que la carga (si no se ha manipulado) se generó dentro de esa ventana, dar o tomar un pequeño factor de Fudge en cada lado.</span><span class="sxs-lookup"><span data-stu-id="9ef98-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
