---
title: Derivación de subclaves y cifrado autenticado en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la implementación de ASP.NET Core la derivación de subclaves de protección de datos y el cifrado autenticado.
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 619a848eb96faab6997f9ddbf4d62a1e04ee66b1
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060376"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Derivación de subclaves y cifrado autenticado en ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

La mayoría de las claves del anillo clave contendrán alguna forma de entropía y tendrán información algorítmica que indica "cifrado de modo CBC + validación HMAC" o "cifrado y validación de GCM". En estos casos, hacemos referencia a la entropía incrustada como el material de creación de claves maestro (o KM) para esta clave y realizamos una función de derivación de claves para derivar las claves que se usarán para las operaciones criptográficas reales.

> [!NOTE]
> Las claves son abstractas y es posible que una implementación personalizada no se comporte como se indica a continuación. Si la clave proporciona su propia implementación de `IAuthenticatedEncryptor` en lugar de usar uno de nuestros generadores integrados, ya no se aplica el mecanismo descrito en esta sección.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Derivación de subclaves y datos autenticados adicionales

La `IAuthenticatedEncryptor` interfaz actúa como la interfaz principal para todas las operaciones de cifrado autenticadas. Su `Encrypt` método toma dos búferes: Plaintext y additionalAuthenticatedData (AAD). El contenido de texto no cifrado fluye sin modificar la llamada a `IDataProtector.Protect` , pero el sistema genera AAD y consta de tres componentes:

1. El encabezado mágico de 32 bits 09 F0 C9 F0 que identifica esta versión del sistema de protección de datos.

2. Identificador de clave de 128 bits.

3. Cadena de longitud variable formada a partir de la cadena propósito que creó el objeto `IDataProtector` que está realizando esta operación.

Dado que AAD es único para la tupla de los tres componentes, podemos usarlo para derivar nuevas claves de KM en lugar de usar KM en todas nuestras operaciones criptográficas. Para cada llamada a `IAuthenticatedEncryptor.Encrypt` , se produce el siguiente proceso de derivación de claves:

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

Aquí, llamamos a NIST SP800-108 KDF en el modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con los siguientes parámetros:

* Clave de derivación de claves (KDK) =`K_M`

* PRF = HMACSHA512

* etiqueta = additionalAuthenticatedData

* contexto = contextHeader | | keyModifier

El encabezado de contexto es de longitud variable y básicamente sirve como huella digital de los algoritmos para los que se van a derivar `K_E` y `K_H` . El modificador de clave es una cadena de 128 bits generada aleatoriamente para cada llamada a `Encrypt` y sirve para garantizar una probabilidad abrumadora de que ke y KH sean únicos para esta operación de cifrado de autenticación específica, aunque todas las demás entradas de KdF sean constantes.

En el caso del cifrado de modo CBC y las operaciones de validación HMAC, `| K_E |` es la longitud de la clave de cifrado de bloques simétricos y `| K_H |` es el tamaño de síntesis de la rutina HMAC. Para el cifrado y las operaciones de validación de GCM, `| K_H | = 0` .

## <a name="cbc-mode-encryption--hmac-validation"></a>Cifrado de modo CBC + validación HMAC

Una vez que `K_E` se genera mediante el mecanismo anterior, se genera un vector de inicialización aleatorio y se ejecuta el algoritmo de cifrado de bloque simétrico para cifrar el texto no cifrado. El vector de inicialización y el texto cifrado se ejecutan a través de la rutina HMAC inicializada con la clave `K_H` para producir el equipo Mac. Este proceso y el valor devuelto se representan gráficamente a continuación.

![Proceso y devolución de modo CBC](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> La `IDataProtector.Protect` implementación [anteponerá el encabezado mágico y el ID](xref:security/data-protection/implementation/authenticated-encryption-details) . de clave a la salida antes de devolverlos al autor de la llamada. Dado que el encabezado mágico y el ID. de clave forman parte implícitamente de [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), y dado que el modificador de clave se introduce como entrada para KDF, esto significa que el equipo Mac autentica cada uno de los bytes de la carga devuelta final.

## <a name="galoiscounter-mode-encryption--validation"></a>Cifrado de modo de Galois/contador + validación

Una vez que `K_E` se genera mediante el mecanismo anterior, se genera un nonce aleatorio de 96 bits y se ejecuta el algoritmo de cifrado de bloques simétricos para cifrar el texto sin formato y generar la etiqueta de autenticación de 128 bits.

![Proceso y devolución de modo GCM](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> Aunque GCM es compatible de forma nativa con el concepto de AAD, todavía estamos alimentando AAD solo para el KDF original, y optando por pasar una cadena vacía a GCM para su parámetro de AAD. El motivo de esto es el doble. En primer lugar, [para admitir la agilidad](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) , nunca queremos usar `K_M` directamente como clave de cifrado. Además, GCM impone requisitos de unicidad muy estrictos en sus entradas. La probabilidad de que la rutina de cifrado de GCM se invoque en dos o más conjuntos distintos de datos de entrada con el mismo par (clave, nonce) no debe superar los 2 ^ 32. Si se corrige `K_E` , no se pueden realizar más de 2 ^ 32 operaciones de cifrado antes de que se ejecute afoul del límite de 2 ^-32. Esto podría parecer un gran número de operaciones, pero un servidor Web de tráfico alto puede pasar por 4 mil millones solicitudes en cuestión de días, dentro de la duración normal de estas claves. Para mantener la compatibilidad con el límite de probabilidad de 2 ^-32, se sigue usando un modificador de clave de 128 bits y un nonce de 96 bits, que amplía radicalmente el recuento de operaciones utilizable para cualquier `K_M` . Para simplificar el diseño, se comparte la ruta de acceso del código de KDF entre las operaciones CBC y GCM, y dado que AAD ya se tiene en cuenta en el KDF, no es necesario reenviarlo a la rutina GCM.
