---
title: Administración y duración de las claves de protección de datos en ASP.NET Core
author: rick-anderson
description: Más información sobre la duración y la administración de claves de protección de datos en ASP.NET Core.
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: b39187d93247dc83c34bbbe6ec6accfd77108794
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021385"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Administración y duración de las claves de protección de datos en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Administración de claves

La aplicación intenta detectar su entorno operativo y controlar la configuración de claves por sí mismo.

1. Si la aplicación se hospeda en [aplicaciones de Azure](https://azure.microsoft.com/services/app-service/), las claves se conservan en la carpeta *%Home%\ASP.NET\DataProtection-Keys* Esta carpeta está respaldada por el almacenamiento de red y se sincroniza en todas las máquinas que hospedan la aplicación.
   * Las claves no están protegidas en reposo.
   * La carpeta de *claves de protección* de la información proporciona el anillo de claves a todas las instancias de una aplicación en una sola ranura de implementación.
   * Las ranuras de implementación independientes, por ejemplo, almacenamiento provisional y producción, no comparten ningún anillo de clave. Al intercambiar entre las ranuras de implementación, por ejemplo, el intercambio de ensayo a producción o el uso de pruebas A/B, cualquier aplicación que use la protección de datos no podrá descifrar los datos almacenados mediante el anillo de claves dentro de la ranura anterior. Esto conduce a los usuarios que se registran en una aplicación que usa la autenticación de ASP.NET Core estándar cookie , ya que usa la protección de datos para proteger sus cookie s. Si desea usar anillos de claves independientes de la ranura, use un proveedor de anillo de claves externo, como Azure Blob Storage, Azure Key Vault, un almacén de SQL o caché en Redis.

1. Si el perfil de usuario está disponible, las claves se conservan en la carpeta *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* Si el sistema operativo es Windows, las claves se cifran en reposo mediante DPAPI.

   También se debe habilitar el [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones. El valor predeterminado de `setProfileEnvironment` es `true`. En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`. Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:

   1. Vaya a la carpeta *%windir%/system32/inetsrv/config*.
   1. Abra el archivo *applicationHost.config*.
   1. Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
   1. Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.

1. Si la aplicación se hospeda en IIS, las claves se conservan en el registro HKLM en una clave del registro especial que solo se agregan en la cuenta de proceso de trabajo. Las claves se cifran en reposo con DPAPI.

1. Si ninguna de estas condiciones coincide, las claves no se conservan fuera del proceso actual. Cuando el proceso se cierra, se pierden todas las claves generadas.

El desarrollador está siempre en control total y puede invalidar cómo y dónde se almacenan las claves. Las tres primeras opciones anteriores deben proporcionar buenos valores predeterminados para la mayoría de las aplicaciones, de forma similar a como las **\<machineKey>** rutinas de generación automática de ASP.net funcionaron en el pasado. La opción final de reserva es el único escenario que requiere que el desarrollador especifique la [configuración](xref:security/data-protection/configuration/overview) por adelantado si desea la persistencia de la clave, pero esta reserva solo se produce en situaciones excepcionales.

Al hospedar en un contenedor de Docker, las claves deben conservarse en una carpeta que sea un volumen de Docker (un volumen compartido o un volumen montado en host que persista más allá de la duración del contenedor) o en un proveedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o [Redis](https://redis.io/). Un proveedor externo también es útil en escenarios de granjas de servidores Web si las aplicaciones no pueden tener acceso a un volumen de red compartido (consulte [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) para obtener más información).

> [!WARNING]
> Si el desarrollador reemplaza las reglas descritas anteriormente y apunta al sistema de protección de datos en un repositorio de claves específico, se deshabilita el cifrado automático de las claves en reposo. La protección en reposo se puede volver a habilitar a través de la [configuración](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Vigencia de la clave

De forma predeterminada, las claves tienen una duración de 90 días. Cuando expira una clave, la aplicación genera automáticamente una nueva clave y establece la nueva clave como la clave activa. Siempre que las claves retiradas permanezcan en el sistema, la aplicación puede descifrar los datos protegidos con ellos. Consulte [Administración de claves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) para obtener más información.

## <a name="default-algorithms"></a>Algoritmos predeterminados

El algoritmo de protección de carga predeterminado usado es AES-256-CBC para la confidencialidad y HMACSHA256 para la autenticidad. Una clave maestra de 512 bits modificada cada 90 días se usa para derivar las dos subclaves que se usan para estos algoritmos por carga. Vea [derivación de subclaves](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) para obtener más información.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
