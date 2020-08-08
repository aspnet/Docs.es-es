---
title: Administración de claves en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la implementación de las API de administración de claves de protección de datos ASP.NET Core.
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: c81e328d8774bfbd1309f854715fcda2152f9eeb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021216"
---
# <a name="key-management-in-aspnet-core"></a>Administración de claves en ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

El sistema de protección de datos administra automáticamente la duración de las claves maestras utilizadas para proteger y desproteger las cargas. Cada clave puede existir en una de estas cuatro fases:

* Creado: la clave existe en el anillo de claves, pero aún no se ha activado. La clave no debe usarse para nuevas operaciones de protección hasta que haya transcurrido el tiempo suficiente para que la clave haya tenido la oportunidad de propagarse a todas las máquinas que están consumiendo este anillo de claves.

* Activo: la clave existe en el anillo de claves y se debe usar para todas las nuevas operaciones de protección.

* Expired: la clave ha ejecutado su duración natural y ya no debe usarse para nuevas operaciones de protección.

* Revocado: la clave está en peligro y no debe usarse para nuevas operaciones de protección.

Las claves creadas, activas y expiradas se pueden usar para desproteger las cargas entrantes. De forma predeterminada, las claves revocadas no se pueden usar para desproteger las cargas, pero el desarrollador de la aplicación puede [invalidar este comportamiento](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) si es necesario.

>[!WARNING]
> El desarrollador podría verse tentado a eliminar una clave del anillo de claves (por ejemplo, eliminando el archivo correspondiente del sistema de archivos). En ese momento, todos los datos protegidos por la clave son permanentemente descifrables y no hay ninguna invalidación de emergencia como sucede con las claves revocadas. La eliminación de una clave es realmente un comportamiento destructivo y, por consiguiente, el sistema de protección de datos no expone ninguna API de primera clase para realizar esta operación.

## <a name="default-key-selection"></a>Selección de clave predeterminada

Cuando el sistema de protección de datos lee el anillo de claves del repositorio de respaldo, intentará buscar una clave "predeterminada" desde el anillo de claves. La clave predeterminada se usa para las nuevas operaciones de protección.

La heurística general es que el sistema de protección de datos elige la clave con la fecha de activación más reciente como la clave predeterminada. (Hay un factor de Fudge pequeño para permitir el sesgo de reloj entre servidores). Si la clave ha expirado o se ha revocado, y si la aplicación no ha deshabilitado la generación automática de claves, se generará una nueva clave con la activación inmediata según la Directiva de [expiración de clave y](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) la Directiva gradual siguiente.

El motivo por el que el sistema de protección de datos genera una nueva clave inmediatamente en lugar de revertir a una clave diferente es que la nueva generación de claves debe tratarse como una expiración implícita de todas las claves que se activaron antes de la nueva clave. La idea general es que las nuevas claves se hayan configurado con distintos algoritmos o mecanismos de cifrado en reposo que las claves antiguas, y el sistema debe preferir la configuración actual en lugar de revertirla.

Hay una excepción. Si el desarrollador de la aplicación ha [deshabilitado la generación automática de claves](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), el sistema de protección de datos debe elegir algo como clave predeterminada. En este escenario de reserva, el sistema elegirá la clave no revocada con la fecha de activación más reciente, con preferencia a las claves que han tenido tiempo de propagarse a otras máquinas del clúster. El sistema de reserva puede acabar eligiendo una clave predeterminada caducada como resultado. El sistema de reserva nunca elegirá una clave revocada como clave predeterminada y, si el anillo de claves está vacío o se ha revocado cada clave, el sistema generará un error tras la inicialización.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Expiración de clave y gradual

Cuando se crea una clave, se le asigna automáticamente una fecha de activación de {Now + 2 días} y una fecha de expiración de {Now + 90 días}. El retraso de 2 días antes de la activación proporciona el tiempo clave para propagarse a través del sistema. Es decir, permite que otras aplicaciones que apuntan a la memoria auxiliar observen la clave en el siguiente período de actualización automática, lo que maximiza el riesgo de que cuando el anillo de claves se active, se propague a todas las aplicaciones que puedan necesitar usarlo.

Si la clave predeterminada expirará en un plazo de 2 días y el anillo de claves aún no tiene una clave que esté activa cuando expire la clave predeterminada, el sistema de protección de datos conservará automáticamente una nueva clave en el anillo de claves. Esta nueva clave tiene una fecha de activación de {fecha de expiración de la clave predeterminada} y una fecha de expiración de {Now + 90 días}. Esto permite al sistema revertir las claves de forma periódica sin interrupción del servicio.

Puede haber circunstancias en las que se creará una clave con activación inmediata. Un ejemplo sería cuando la aplicación no se ha ejecutado durante un tiempo y todas las claves del anillo de claves han expirado. Cuando esto sucede, a la clave se le asigna una fecha de activación de {Now} sin el retraso de activación de 2 días normal.

La duración de la clave predeterminada es de 90 días, aunque se puede configurar como en el ejemplo siguiente.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Un administrador también puede cambiar el valor predeterminado de todo el sistema, aunque una llamada explícita a `SetDefaultKeyLifetime` invalidará cualquier directiva de todo el sistema. La duración de la clave predeterminada no puede ser inferior a 7 días.

## <a name="automatic-key-ring-refresh"></a>Actualización automática de Key Ring

Cuando el sistema de protección de datos se inicializa, lee el anillo de claves del repositorio subyacente y lo almacena en memoria caché. Esta memoria caché permite que las operaciones de protección y desprotección continúen sin tener que golpear la memoria auxiliar. El sistema comprobará automáticamente la memoria auxiliar de los cambios aproximadamente cada 24 horas o cuando expire la clave predeterminada actual, lo que suceda primero.

>[!WARNING]
> En raras ocasiones, los desarrolladores deben usar las API de administración de claves directamente. El sistema de protección de datos llevará a cabo la administración automática de claves como se describió anteriormente.

El sistema de protección de datos expone una interfaz `IKeyManager` que se puede usar para inspeccionar y realizar cambios en el anillo de claves. El sistema DI que proporcionó la instancia de `IDataProtectionProvider` también puede proporcionar una instancia de `IKeyManager` para su consumo. Como alternativa, puede extraer el `IKeyManager` directo de `IServiceProvider` como en el ejemplo siguiente.

Cualquier operación que modifique el anillo clave (al crear una nueva clave explícitamente o realizar una revocación) invalidará la caché en memoria. La siguiente llamada a `Protect` o hará `Unprotect` que el sistema de protección de datos vuelva a leer el anillo de claves y vuelva a crear la memoria caché.

En el ejemplo siguiente se muestra cómo usar la `IKeyManager` interfaz para inspeccionar y manipular el anillo de claves, incluida la revocación de las claves existentes y la generación manual de una nueva clave.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Almacenamiento de claves

El sistema de protección de datos tiene una heurística en la que intenta deducir automáticamente una ubicación de almacenamiento de claves adecuada y el mecanismo de cifrado en reposo. El desarrollador de la aplicación también puede configurar el mecanismo de persistencia de claves. En los documentos siguientes se describen las implementaciones integradas de estos mecanismos:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
