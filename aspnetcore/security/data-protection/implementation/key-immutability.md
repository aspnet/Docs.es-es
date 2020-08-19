---
title: Inmutabilidad de claves y configuración de claves en ASP.NET Core
author: rick-anderson
description: Conozca los detalles de implementación de las API de inmutabilidad de la clave de protección de datos de ASP.NET Core.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 32925a3ef6d52a7be3a852cefe670c15d545b490
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627629"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Inmutabilidad de claves y configuración de claves en ASP.NET Core

Una vez que un objeto se conserva en la memoria auxiliar, su representación es fija para siempre. Se pueden agregar nuevos datos a la memoria auxiliar, pero nunca se pueden mutar los datos existentes. El propósito principal de este comportamiento es evitar daños en los datos.

Una consecuencia de este comportamiento es que una vez que una clave se escribe en la memoria auxiliar, es inmutable. Sus fechas de creación, activación y expiración no se pueden cambiar nunca, aunque se pueden revocar mediante `IKeyManager` . Además, la información algorítmica subyacente, el material de creación de claves maestra y el cifrado en las propiedades de REST también son inmutables.

Si el desarrollador cambia cualquier valor de configuración que afecte a la persistencia de claves, esos cambios no entrarán en vigor hasta la próxima vez que se genere una clave, ya sea a través de una llamada explícita a `IKeyManager.CreateNewKey` o a través del comportamiento de la [generación automática de claves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) del sistema de protección de datos. Los valores que afectan a la persistencia de claves son los siguientes:

* [La duración de la clave predeterminada](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [El mecanismo de cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest)

* [La información algorítmica incluida en la clave](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Si necesita que esta configuración se inicie antes que el siguiente tiempo de lanzamiento de claves automática, considere la posibilidad de realizar una llamada explícita a `IKeyManager.CreateNewKey` para forzar la creación de una nueva clave. No olvide proporcionar una fecha de activación explícita ({Now + 2 días} es una buena regla general para dejar tiempo para que se propague el cambio) y la fecha de expiración en la llamada.

>[!TIP]
> Todas las aplicaciones que tocan el repositorio deben especificar la misma configuración con los `IDataProtectionBuilder` métodos de extensión. De lo contrario, las propiedades de la clave conservada dependerán de la aplicación concreta que invocó las rutinas de generación de claves.
