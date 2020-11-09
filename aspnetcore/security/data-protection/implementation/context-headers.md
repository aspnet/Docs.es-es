---
title: Encabezados de contexto en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación de ASP.NET Core encabezados de contexto de protección de datos.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051725"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="b4009-103">Encabezados de contexto en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4009-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="b4009-104">Antecedentes y teoría</span><span class="sxs-lookup"><span data-stu-id="b4009-104">Background and theory</span></span>

<span data-ttu-id="b4009-105">En el sistema de protección de datos, una "clave" hace referencia a un objeto que puede proporcionar servicios de cifrado autenticados.</span><span class="sxs-lookup"><span data-stu-id="b4009-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="b4009-106">Cada clave se identifica mediante un identificador único (un GUID) y incluye información algorítmica y material Entropic.</span><span class="sxs-lookup"><span data-stu-id="b4009-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="b4009-107">Está previsto que cada clave lleve una entropía única, pero el sistema no puede aplicar esto, y también tenemos que tener en cuenta a los desarrolladores que podrían cambiar el anillo de claves manualmente modificando la información algorítmica de una clave existente en el anillo de claves.</span><span class="sxs-lookup"><span data-stu-id="b4009-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="b4009-108">Para alcanzar los requisitos de seguridad dados estos casos, el sistema de protección de datos tiene un concepto de [agilidad criptográfica](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), que permite usar de forma segura un único valor de Entropic en varios algoritmos criptográficos.</span><span class="sxs-lookup"><span data-stu-id="b4009-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="b4009-109">La mayoría de los sistemas que admiten la agilidad criptográfica lo hacen mediante la inclusión de información de identificación sobre el algoritmo dentro de la carga.</span><span class="sxs-lookup"><span data-stu-id="b4009-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="b4009-110">El OID del algoritmo suele ser un buen candidato para esto.</span><span class="sxs-lookup"><span data-stu-id="b4009-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="b4009-111">Sin embargo, un problema en el que se produjo es que hay varias maneras de especificar el mismo algoritmo: "AES" (CNG) y las clases administradas AES, AesManaged, AesCryptoServiceProvider, AesCng y RijndaelManaged (determinados parámetros específicos) son realmente lo mismo, y es necesario mantener una asignación de todos ellos al OID correcto.</span><span class="sxs-lookup"><span data-stu-id="b4009-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="b4009-112">Si un desarrollador desea proporcionar un algoritmo personalizado (o incluso otra implementación de AES!), deberá indicarnos su OID.</span><span class="sxs-lookup"><span data-stu-id="b4009-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="b4009-113">Este paso de registro adicional hace que la configuración del sistema sea especialmente complicada.</span><span class="sxs-lookup"><span data-stu-id="b4009-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="b4009-114">Volviendo atrás, decidimos que estamos llegando al problema de la dirección equivocada.</span><span class="sxs-lookup"><span data-stu-id="b4009-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="b4009-115">Un OID le indica cuál es el algoritmo, pero realmente no le interesa.</span><span class="sxs-lookup"><span data-stu-id="b4009-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="b4009-116">Si necesitamos usar un único valor de Entropic de forma segura en dos algoritmos diferentes, no es necesario saber cuáles son los algoritmos realmente.</span><span class="sxs-lookup"><span data-stu-id="b4009-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="b4009-117">Lo que realmente nos importa es cómo se comportan.</span><span class="sxs-lookup"><span data-stu-id="b4009-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="b4009-118">Los algoritmos de cifrado de bloques simétricos decentes también son una permutación pseudoaleatorio (PRP) segura: corrija las entradas (clave, modo de encadenamiento, IV, texto no cifrado) y la salida de texto cifrado con una probabilidad abrumadora distinta de cualquier otro algoritmo de cifrado de bloques simétricos con las mismas entradas.</span><span class="sxs-lookup"><span data-stu-id="b4009-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="b4009-119">Del mismo modo, cualquier función hash con clave decente es también una sólida función pseudoaleatorios (PRF) y, si se especifica un conjunto de entrada fijo, su salida será muy distinta de cualquier otra función hash con clave.</span><span class="sxs-lookup"><span data-stu-id="b4009-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="b4009-120">Usamos este concepto de Strong PRPs y PRFs para crear un encabezado de contexto.</span><span class="sxs-lookup"><span data-stu-id="b4009-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="b4009-121">Este encabezado de contexto actúa esencialmente como una huella digital estable en los algoritmos que se usan en una operación determinada y proporciona la agilidad criptográfica necesaria para el sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="b4009-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="b4009-122">Este encabezado es reproducible y se usa más adelante como parte del [proceso de derivación de subclaves](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="b4009-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="b4009-123">Hay dos maneras diferentes de compilar el encabezado de contexto en función de los modos de funcionamiento de los algoritmos subyacentes.</span><span class="sxs-lookup"><span data-stu-id="b4009-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="b4009-124">Cifrado de modo CBC + autenticación HMAC</span><span class="sxs-lookup"><span data-stu-id="b4009-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="b4009-125">El encabezado de contexto consta de los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="b4009-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="b4009-126">[16 bits] El valor 00 00, que es un marcador que significa "CBC Encryption + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="b4009-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="b4009-127">[32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.</span><span class="sxs-lookup"><span data-stu-id="b4009-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="b4009-128">[32 bits] Tamaño de bloque (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.</span><span class="sxs-lookup"><span data-stu-id="b4009-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="b4009-129">[32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="b4009-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="b4009-130">(Actualmente, el tamaño de la clave coincide siempre con el tamaño de síntesis).</span><span class="sxs-lookup"><span data-stu-id="b4009-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="b4009-131">[32 bits] Tamaño de síntesis (en bytes, Big-endian) del algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="b4009-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="b4009-132">`EncCBC(K_E, IV, "")`, que es la salida del algoritmo de cifrado de bloques simétricos dada una entrada de cadena vacía y donde IV es un vector de todo cero.</span><span class="sxs-lookup"><span data-stu-id="b4009-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="b4009-133">`K_E`A continuación se describe la construcción de.</span><span class="sxs-lookup"><span data-stu-id="b4009-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="b4009-134">`MAC(K_H, "")`, que es la salida del algoritmo HMAC dada una entrada de cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="b4009-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="b4009-135">`K_H`A continuación se describe la construcción de.</span><span class="sxs-lookup"><span data-stu-id="b4009-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="b4009-136">Idealmente, podríamos pasar todos los vectores de cero para `K_E` y `K_H` .</span><span class="sxs-lookup"><span data-stu-id="b4009-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="b4009-137">Sin embargo, queremos evitar la situación en la que el algoritmo subyacente comprueba la existencia de claves débiles antes de realizar cualquier operación (en particular, DES y 3DES), lo que impide el uso de un patrón simple o repetible como un vector de todo cero.</span><span class="sxs-lookup"><span data-stu-id="b4009-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="b4009-138">En su lugar, usamos NIST SP800-108 KDF en modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con una clave, etiqueta y contexto de longitud cero y HMACSHA512 como el PRF subyacente.</span><span class="sxs-lookup"><span data-stu-id="b4009-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="b4009-139">Se derivan `| K_E | + | K_H |` bytes de salida y, a continuación, se descompone el resultado en `K_E` y en `K_H` sí mismos.</span><span class="sxs-lookup"><span data-stu-id="b4009-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="b4009-140">Matemáticamente, esto se representa como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="b4009-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="b4009-141">Ejemplo: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="b4009-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="b4009-142">Como ejemplo, considere el caso en el que el algoritmo de cifrado de bloques simétricos es AES-192-CBC y el algoritmo de validación es HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="b4009-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="b4009-143">El sistema generaría el encabezado de contexto mediante los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="b4009-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="b4009-144">En primer lugar, permita `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , donde `| K_E | = 192 bits` y `| K_H | = 256 bits` según los algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="b4009-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="b4009-145">Esto conduce a `K_E = 5BB6..21DD` y `K_H = A04A..00A9` en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b4009-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="b4009-146">A continuación, Compute `Enc_CBC (K_E, IV, "")` para AES-192-CBC `IV = 0*` y como se indica a continuación `K_E` .</span><span class="sxs-lookup"><span data-stu-id="b4009-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="b4009-147">A continuación, Compute `MAC(K_H, "")` para HMACSHA256 proporcionado `K_H` como se indicó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="b4009-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="b4009-148">Esto genera el encabezado de contexto completo a continuación:</span><span class="sxs-lookup"><span data-stu-id="b4009-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="b4009-149">Este encabezado de contexto es la huella digital del par de algoritmos de cifrado autenticados (cifrado AES-192-CBC + validación HMACSHA256).</span><span class="sxs-lookup"><span data-stu-id="b4009-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="b4009-150">Los componentes, como se describió [anteriormente](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="b4009-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="b4009-151">el marcador `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="b4009-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="b4009-152">la longitud de la clave de cifrado de bloques `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="b4009-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="b4009-153">tamaño de bloque de cifrado de bloques `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="b4009-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="b4009-154">la longitud de la clave HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="b4009-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="b4009-155">el tamaño de síntesis de HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="b4009-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="b4009-156">la salida de cifrado de bloqueo PRP `(F4 74 - DB 6F)` y</span><span class="sxs-lookup"><span data-stu-id="b4009-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="b4009-157">salida de HMAC PRF `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="b4009-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="b4009-158">El encabezado de contexto de autenticación con cifrado de modo CBC + HMAC se crea de la misma forma, independientemente de si las implementaciones de algoritmos las proporciona la CNG de Windows o los tipos de SymmetricAlgorithm y KeyedHashAlgorithm administrados.</span><span class="sxs-lookup"><span data-stu-id="b4009-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="b4009-159">Esto permite que las aplicaciones que se ejecutan en distintos sistemas operativos generen de forma confiable el mismo encabezado de contexto, aunque las implementaciones de los algoritmos difieran entre los sistemas operativos.</span><span class="sxs-lookup"><span data-stu-id="b4009-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="b4009-160">(En la práctica, el KeyedHashAlgorithm no tiene que ser un HMAC adecuado.</span><span class="sxs-lookup"><span data-stu-id="b4009-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="b4009-161">Puede ser cualquier tipo de algoritmo hash con clave).</span><span class="sxs-lookup"><span data-stu-id="b4009-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="b4009-162">Ejemplo: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="b4009-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="b4009-163">En primer lugar, permita `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , donde `| K_E | = 192 bits` y `| K_H | = 160 bits` según los algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="b4009-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="b4009-164">Esto conduce a `K_E = A219..E2BB` y `K_H = DC4A..B464` en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b4009-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="b4009-165">A continuación, calcule `Enc_CBC (K_E, IV, "")` para 3DES-192-CBC `IV = 0*` y como se indica a continuación `K_E` .</span><span class="sxs-lookup"><span data-stu-id="b4009-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="b4009-166">A continuación, Compute `MAC(K_H, "")` para HMACSHA1 proporcionado `K_H` como se indicó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="b4009-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="b4009-167">Esto genera el encabezado de contexto completo, que es una huella digital del par de algoritmos de cifrado autenticado (cifrado 3DES-192-CBC Encryption + HMACSHA1), que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="b4009-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="b4009-168">Los componentes se desglosan de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="b4009-168">The components break down as follows:</span></span>

* <span data-ttu-id="b4009-169">el marcador `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="b4009-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="b4009-170">la longitud de la clave de cifrado de bloques `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="b4009-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="b4009-171">tamaño de bloque de cifrado de bloques `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="b4009-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="b4009-172">la longitud de la clave HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="b4009-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="b4009-173">el tamaño de síntesis de HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="b4009-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="b4009-174">la salida de cifrado de bloqueo PRP `(AB B1 - E1 0E)` y</span><span class="sxs-lookup"><span data-stu-id="b4009-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="b4009-175">salida de HMAC PRF `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="b4009-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="b4009-176">Cifrado de modo de Galois/contador + autenticación</span><span class="sxs-lookup"><span data-stu-id="b4009-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="b4009-177">El encabezado de contexto consta de los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="b4009-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="b4009-178">[16 bits] El valor 00 01, que es un marcador que indica "cifrado de GCM + autenticación".</span><span class="sxs-lookup"><span data-stu-id="b4009-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="b4009-179">[32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.</span><span class="sxs-lookup"><span data-stu-id="b4009-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="b4009-180">[32 bits] El tamaño de nonce (en bytes, Big-endian) utilizado durante las operaciones de cifrado autenticado.</span><span class="sxs-lookup"><span data-stu-id="b4009-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="b4009-181">(En el caso de nuestro sistema, este problema se ha corregido en nonce size = 96 bits).</span><span class="sxs-lookup"><span data-stu-id="b4009-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="b4009-182">[32 bits] Tamaño de bloque (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.</span><span class="sxs-lookup"><span data-stu-id="b4009-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="b4009-183">(Para GCM, se fija en tamaño de bloque = 128 bits).</span><span class="sxs-lookup"><span data-stu-id="b4009-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="b4009-184">[32 bits] El tamaño de la etiqueta de autenticación (en bytes, Big-endian) generado por la función de cifrado autenticado.</span><span class="sxs-lookup"><span data-stu-id="b4009-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="b4009-185">(En nuestro sistema, esto se corrigió en tamaño de etiqueta = 128 bits).</span><span class="sxs-lookup"><span data-stu-id="b4009-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="b4009-186">[128 bits] La etiqueta de `Enc_GCM (K_E, nonce, "")` , que es la salida del algoritmo de cifrado de bloques simétricos dada una entrada de cadena vacía y en la que el valor de nonce es un vector de 96 bits todo-cero.</span><span class="sxs-lookup"><span data-stu-id="b4009-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="b4009-187">`K_E` se deriva mediante el mismo mecanismo que en el escenario CBC Encryption + HMAC Authentication.</span><span class="sxs-lookup"><span data-stu-id="b4009-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="b4009-188">Sin embargo, puesto que no hay nada `K_H` en juego, esencialmente tenemos `| K_H | = 0` y el algoritmo se contrae en el formulario siguiente.</span><span class="sxs-lookup"><span data-stu-id="b4009-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="b4009-189">Ejemplo: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="b4009-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="b4009-190">En primer lugar, supongamos `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` dónde `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="b4009-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="b4009-191">A continuación, calcule la etiqueta de autenticación de `Enc_GCM (K_E, nonce, "")` para AES-256-GCM `nonce = 096` y como se indica a continuación `K_E` .</span><span class="sxs-lookup"><span data-stu-id="b4009-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="b4009-192">Esto genera el encabezado de contexto completo a continuación:</span><span class="sxs-lookup"><span data-stu-id="b4009-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="b4009-193">Los componentes se desglosan de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="b4009-193">The components break down as follows:</span></span>

* <span data-ttu-id="b4009-194">el marcador `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="b4009-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="b4009-195">la longitud de la clave de cifrado de bloques `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="b4009-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="b4009-196">el tamaño del nonce `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="b4009-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="b4009-197">tamaño de bloque de cifrado de bloques `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="b4009-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="b4009-198">el tamaño de la etiqueta de autenticación `(00 00 00 10)` y</span><span class="sxs-lookup"><span data-stu-id="b4009-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="b4009-199">la etiqueta de autenticación de la ejecución del cifrado de bloque `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="b4009-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
