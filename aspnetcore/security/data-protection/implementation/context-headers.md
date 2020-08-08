---
title: Encabezados de contexto en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación de ASP.NET Core encabezados de contexto de protección de datos.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 572f930dbf78aaef1ed47d1a154b5ba56633b4f1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018824"
---
# <a name="context-headers-in-aspnet-core"></a>Encabezados de contexto en ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Antecedentes y teoría

En el sistema de protección de datos, una "clave" hace referencia a un objeto que puede proporcionar servicios de cifrado autenticados. Cada clave se identifica mediante un identificador único (un GUID) y incluye información algorítmica y material Entropic. Está previsto que cada clave lleve una entropía única, pero el sistema no puede aplicar esto, y también tenemos que tener en cuenta a los desarrolladores que podrían cambiar el anillo de claves manualmente modificando la información algorítmica de una clave existente en el anillo de claves. Para alcanzar los requisitos de seguridad dados estos casos, el sistema de protección de datos tiene un concepto de [agilidad criptográfica](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), que permite usar de forma segura un único valor de Entropic en varios algoritmos criptográficos.

La mayoría de los sistemas que admiten la agilidad criptográfica lo hacen mediante la inclusión de información de identificación sobre el algoritmo dentro de la carga. El OID del algoritmo suele ser un buen candidato para esto. Sin embargo, un problema en el que se produjo es que hay varias maneras de especificar el mismo algoritmo: "AES" (CNG) y las clases administradas AES, AesManaged, AesCryptoServiceProvider, AesCng y RijndaelManaged (determinados parámetros específicos) son realmente lo mismo, y es necesario mantener una asignación de todos ellos al OID correcto. Si un desarrollador desea proporcionar un algoritmo personalizado (o incluso otra implementación de AES!), deberá indicarnos su OID. Este paso de registro adicional hace que la configuración del sistema sea especialmente complicada.

Volviendo atrás, decidimos que estamos llegando al problema de la dirección equivocada. Un OID le indica cuál es el algoritmo, pero realmente no le interesa. Si necesitamos usar un único valor de Entropic de forma segura en dos algoritmos diferentes, no es necesario saber cuáles son los algoritmos realmente. Lo que realmente nos importa es cómo se comportan. Los algoritmos de cifrado de bloques simétricos decentes también son una permutación pseudoaleatorio (PRP) segura: corrija las entradas (clave, modo de encadenamiento, IV, texto no cifrado) y la salida de texto cifrado con una probabilidad abrumadora distinta de cualquier otro algoritmo de cifrado de bloques simétricos con las mismas entradas. Del mismo modo, cualquier función hash con clave decente es también una sólida función pseudoaleatorios (PRF) y, si se especifica un conjunto de entrada fijo, su salida será muy distinta de cualquier otra función hash con clave.

Usamos este concepto de Strong PRPs y PRFs para crear un encabezado de contexto. Este encabezado de contexto actúa esencialmente como una huella digital estable en los algoritmos que se usan en una operación determinada y proporciona la agilidad criptográfica necesaria para el sistema de protección de datos. Este encabezado es reproducible y se usa más adelante como parte del [proceso de derivación de subclaves](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Hay dos maneras diferentes de compilar el encabezado de contexto en función de los modos de funcionamiento de los algoritmos subyacentes.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Cifrado de modo CBC + autenticación HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

El encabezado de contexto consta de los siguientes componentes:

* [16 bits] El valor 00 00, que es un marcador que significa "CBC Encryption + HMAC Authentication".

* [32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.

* [32 bits] Tamaño de bloque (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.

* [32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo HMAC. (Actualmente, el tamaño de la clave coincide siempre con el tamaño de síntesis).

* [32 bits] Tamaño de síntesis (en bytes, Big-endian) del algoritmo HMAC.

* `EncCBC(K_E, IV, "")`, que es la salida del algoritmo de cifrado de bloques simétricos dada una entrada de cadena vacía y donde IV es un vector de todo cero. `K_E`A continuación se describe la construcción de.

* `MAC(K_H, "")`, que es la salida del algoritmo HMAC dada una entrada de cadena vacía. `K_H`A continuación se describe la construcción de.

Idealmente, podríamos pasar todos los vectores de cero para `K_E` y `K_H` . Sin embargo, queremos evitar la situación en la que el algoritmo subyacente comprueba la existencia de claves débiles antes de realizar cualquier operación (en particular, DES y 3DES), lo que impide el uso de un patrón simple o repetible como un vector de todo cero.

En su lugar, usamos NIST SP800-108 KDF en modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con una clave, etiqueta y contexto de longitud cero y HMACSHA512 como el PRF subyacente. Se derivan `| K_E | + | K_H |` bytes de salida y, a continuación, se descompone el resultado en `K_E` y en `K_H` sí mismos. Matemáticamente, esto se representa como se indica a continuación.

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>Ejemplo: AES-192-CBC + HMACSHA256

Como ejemplo, considere el caso en el que el algoritmo de cifrado de bloques simétricos es AES-192-CBC y el algoritmo de validación es HMACSHA256. El sistema generaría el encabezado de contexto mediante los pasos siguientes.

En primer lugar, permita `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , donde `| K_E | = 192 bits` y `| K_H | = 256 bits` según los algoritmos especificados. Esto conduce a `K_E = 5BB6..21DD` y `K_H = A04A..00A9` en el ejemplo siguiente:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

A continuación, Compute `Enc_CBC (K_E, IV, "")` para AES-192-CBC `IV = 0*` y como se indica a continuación `K_E` .

`result := F474B1872B3B53E4721DE19C0841DB6F`

A continuación, Compute `MAC(K_H, "")` para HMACSHA256 proporcionado `K_H` como se indicó anteriormente.

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

Esto genera el encabezado de contexto completo a continuación:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Este encabezado de contexto es la huella digital del par de algoritmos de cifrado autenticados (cifrado AES-192-CBC + validación HMACSHA256). Los componentes, como se describió [anteriormente](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , son los siguientes:

* el marcador`(00 00)`

* la longitud de la clave de cifrado de bloques`(00 00 00 18)`

* tamaño de bloque de cifrado de bloques`(00 00 00 10)`

* la longitud de la clave HMAC`(00 00 00 20)`

* el tamaño de síntesis de HMAC`(00 00 00 20)`

* la salida de cifrado de bloqueo PRP `(F4 74 - DB 6F)` y

* salida de HMAC PRF `(D4 79 - end)` .

> [!NOTE]
> El encabezado de contexto de autenticación con cifrado de modo CBC + HMAC se crea de la misma forma, independientemente de si las implementaciones de algoritmos las proporciona la CNG de Windows o los tipos de SymmetricAlgorithm y KeyedHashAlgorithm administrados. Esto permite que las aplicaciones que se ejecutan en distintos sistemas operativos generen de forma confiable el mismo encabezado de contexto, aunque las implementaciones de los algoritmos difieran entre los sistemas operativos. (En la práctica, el KeyedHashAlgorithm no tiene que ser un HMAC adecuado. Puede ser cualquier tipo de algoritmo hash con clave).

### <a name="example-3des-192-cbc--hmacsha1"></a>Ejemplo: 3DES-192-CBC + HMACSHA1

En primer lugar, permita `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , donde `| K_E | = 192 bits` y `| K_H | = 160 bits` según los algoritmos especificados. Esto conduce a `K_E = A219..E2BB` y `K_H = DC4A..B464` en el ejemplo siguiente:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

A continuación, calcule `Enc_CBC (K_E, IV, "")` para 3DES-192-CBC `IV = 0*` y como se indica a continuación `K_E` .

`result := ABB100F81E53E10E`

A continuación, Compute `MAC(K_H, "")` para HMACSHA1 proporcionado `K_H` como se indicó anteriormente.

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

Esto genera el encabezado de contexto completo, que es una huella digital del par de algoritmos de cifrado autenticado (cifrado 3DES-192-CBC Encryption + HMACSHA1), que se muestra a continuación:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Los componentes se desglosan de la manera siguiente:

* el marcador`(00 00)`

* la longitud de la clave de cifrado de bloques`(00 00 00 18)`

* tamaño de bloque de cifrado de bloques`(00 00 00 08)`

* la longitud de la clave HMAC`(00 00 00 14)`

* el tamaño de síntesis de HMAC`(00 00 00 14)`

* la salida de cifrado de bloqueo PRP `(AB B1 - E1 0E)` y

* salida de HMAC PRF `(76 EB - end)` .

## <a name="galoiscounter-mode-encryption--authentication"></a>Cifrado de modo de Galois/contador + autenticación

El encabezado de contexto consta de los siguientes componentes:

* [16 bits] El valor 00 01, que es un marcador que indica "cifrado de GCM + autenticación".

* [32 bits] La longitud de clave (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos.

* [32 bits] El tamaño de nonce (en bytes, Big-endian) utilizado durante las operaciones de cifrado autenticado. (En el caso de nuestro sistema, este problema se ha corregido en nonce size = 96 bits).

* [32 bits] Tamaño de bloque (en bytes, Big-endian) del algoritmo de cifrado de bloques simétricos. (Para GCM, se fija en tamaño de bloque = 128 bits).

* [32 bits] El tamaño de la etiqueta de autenticación (en bytes, Big-endian) generado por la función de cifrado autenticado. (En nuestro sistema, esto se corrigió en tamaño de etiqueta = 128 bits).

* [128 bits] La etiqueta de `Enc_GCM (K_E, nonce, "")` , que es la salida del algoritmo de cifrado de bloques simétricos dada una entrada de cadena vacía y en la que el valor de nonce es un vector de 96 bits todo-cero.

`K_E`se deriva mediante el mismo mecanismo que en el escenario CBC Encryption + HMAC Authentication. Sin embargo, puesto que no hay nada `K_H` en juego, esencialmente tenemos `| K_H | = 0` y el algoritmo se contrae en el formulario siguiente.

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>Ejemplo: AES-256-GCM

En primer lugar, supongamos `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` dónde `| K_E | = 256 bits` .

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

A continuación, calcule la etiqueta de autenticación de `Enc_GCM (K_E, nonce, "")` para AES-256-GCM `nonce = 096` y como se indica a continuación `K_E` .

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

Esto genera el encabezado de contexto completo a continuación:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Los componentes se desglosan de la manera siguiente:

* el marcador`(00 01)`

* la longitud de la clave de cifrado de bloques`(00 00 00 20)`

* el tamaño del nonce`(00 00 00 0C)`

* tamaño de bloque de cifrado de bloques`(00 00 00 10)`

* el tamaño de la etiqueta de autenticación `(00 00 00 10)` y

* la etiqueta de autenticación de la ejecución del cifrado de bloque `(E7 DC - end)` .
