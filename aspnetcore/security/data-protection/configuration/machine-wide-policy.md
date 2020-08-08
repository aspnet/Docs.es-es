---
title: Compatibilidad con la Directiva de protección de datos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la compatibilidad con la configuración de una directiva predeterminada de todo el equipo para todas las aplicaciones que consumen ASP.NET Core protección de datos.
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f4b8dc379c0219ff9fc363df55df1103ef40a5ce
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022412"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Compatibilidad con la Directiva de protección de datos en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Cuando se ejecuta en Windows, el sistema de protección de datos tiene compatibilidad limitada para establecer una directiva predeterminada de todo el equipo para todas las aplicaciones que consumen ASP.NET Core protección de datos. La idea general es que es posible que un administrador desee cambiar una configuración predeterminada, como los algoritmos usados o la duración de la clave, sin necesidad de actualizar manualmente todas las aplicaciones de la máquina.

> [!WARNING]
> El administrador del sistema puede establecer la directiva predeterminada, pero no puede aplicarla. El desarrollador de la aplicación siempre puede reemplazar cualquier valor por uno de su propia elección. La directiva predeterminada solo afecta a las aplicaciones en las que el desarrollador no ha especificado un valor explícito para una configuración.

## <a name="setting-default-policy"></a>Configuración de la directiva predeterminada

Para establecer la directiva predeterminada, un administrador puede establecer valores conocidos en el registro del sistema en la siguiente clave del registro:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Si se encuentra en un sistema operativo de 64 bits y quiere afectar al comportamiento de las aplicaciones de 32 bits, no olvide configurar el equivalente de Wow6432Node de la clave anterior.

Los valores admitidos se muestran a continuación.

| Valor              | Tipo   | Descripción |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | Especifica los algoritmos que se deben usar para la protección de datos. El valor debe ser CNG-CBC, CNG-GCM o administrado y se describe con más detalle a continuación. |
| DefaultKeyLifetime | DWORD  | Especifica la duración de las claves generadas recientemente. El valor se especifica en días y debe ser >= 7. |
| KeyEscrowSinks     | string | Especifica los tipos que se usan para el custodia de claves. El valor es una lista delimitada por signos de punto y coma de receptores de custodia de claves, donde cada elemento de la lista es el nombre calificado con el ensamblado de un tipo que implementa [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Tipos de cifrado

Si EncryptionType es CNG-CBC, el sistema está configurado para usar un cifrado de bloques simétricos de modo CBC para confidencialidad y HMAC para la autenticidad con los servicios proporcionados por CNG de Windows (consulte [especificación de algoritmos CNG personalizados de Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para obtener más detalles). Se admiten los siguientes valores adicionales, cada uno de los cuales corresponde a una propiedad en el tipo CngCbcAuthenticatedEncryptionSettings.

| Valor                       | Tipo   | Descripción |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nombre de un algoritmo de cifrado de bloques simétricos entendido por CNG. Este algoritmo se abre en modo CBC. |
| EncryptionAlgorithmProvider | string | El nombre de la implementación del proveedor de CNG que puede generar el algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | La longitud (en bits) de la clave que se va a derivar para el algoritmo de cifrado de bloques simétricos. |
| HashAlgorithm               | string | Nombre de un algoritmo hash que entiende CNG. Este algoritmo se abre en modo HMAC. |
| HashAlgorithmProvider       | string | El nombre de la implementación del proveedor de CNG que puede generar el algoritmo HashAlgorithm. |

Si EncryptionType es CNG-GCM, el sistema está configurado para usar un cifrado de bloques simétricos de modo Galois/Counter para la confidencialidad y la autenticidad con los servicios proporcionados por CNG de Windows (consulte [especificación de algoritmos CNG personalizados de Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para obtener más detalles). Se admiten los siguientes valores adicionales, cada uno de los cuales corresponde a una propiedad en el tipo CngGcmAuthenticatedEncryptionSettings.

| Valor                       | Tipo   | Descripción |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nombre de un algoritmo de cifrado de bloques simétricos entendido por CNG. Este algoritmo se abre en el modo Galois/Counter. |
| EncryptionAlgorithmProvider | string | El nombre de la implementación del proveedor de CNG que puede generar el algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | La longitud (en bits) de la clave que se va a derivar para el algoritmo de cifrado de bloques simétricos. |

Si EncryptionType está administrado, el sistema está configurado para usar un SymmetricAlgorithm administrado para la confidencialidad y para KeyedHashAlgorithm para la autenticidad (consulte [especificación de algoritmos administrados personalizados](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) para obtener más detalles). Se admiten los siguientes valores adicionales, cada uno de los cuales corresponde a una propiedad en el tipo ManagedAuthenticatedEncryptionSettings.

| Valor                      | Tipo   | Descripción |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | Nombre calificado con el ensamblado de un tipo que implementa SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | La longitud (en bits) de la clave que se va a derivar para el algoritmo de cifrado simétrico. |
| ValidationAlgorithmType    | string | Nombre calificado con el ensamblado de un tipo que implementa KeyedHashAlgorithm. |

Si EncryptionType tiene cualquier otro valor distinto de null o vacío, el sistema de protección de datos inicia una excepción al iniciarse.

> [!WARNING]
> Al configurar una configuración de directiva predeterminada que implique nombres de tipo (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), los tipos deben estar disponibles para la aplicación. Esto significa que para las aplicaciones que se ejecutan en el CLR de escritorio, los ensamblados que contienen estos tipos deben estar presentes en la caché de ensamblados global (GAC). En el caso de las aplicaciones ASP.NET Core que se ejecutan en .NET Core, deben instalarse los paquetes que contienen estos tipos.
