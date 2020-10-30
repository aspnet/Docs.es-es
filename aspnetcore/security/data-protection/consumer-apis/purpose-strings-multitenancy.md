---
title: Jerarquía de propósito y multiinquilino en ASP.NET Core
author: rick-anderson
description: Obtenga información acerca de la jerarquía de cadenas de propósito y multiinquilino en lo que se refiere a las API de protección de datos de ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052791"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Jerarquía de propósito y multiinquilino en ASP.NET Core

Puesto que `IDataProtector` también es implícitamente un `IDataProtectionProvider` , los propósitos se pueden encadenar juntos. En este sentido, `provider.CreateProtector([ "purpose1", "purpose2" ])` es equivalente a `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .

Esto permite algunas relaciones jerárquicas interesantes a través del sistema de protección de datos. En el ejemplo anterior de [contoso. Messaging. SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), el componente SecureMessage puede llamar al `provider.CreateProtector("Contoso.Messaging.SecureMessage")` principio y almacenar en caché el resultado en un `_myProvider` campo privado. Después, los protectores futuros se pueden crear a través `_myProvider.CreateProtector("User: username")` de llamadas a, y estos protectores se utilizarían para proteger los mensajes individuales.

También se puede voltear. Considere una única aplicación lógica que hospede varios inquilinos (un CMS parece razonable) y cada inquilino se puede configurar con su propio sistema de administración de estado y autenticación. La aplicación paraguas tiene un único proveedor maestro y llama a `provider.CreateProtector("Tenant 1")` y `provider.CreateProtector("Tenant 2")` para dar a cada inquilino su propio segmento aislado del sistema de protección de datos. Después, los inquilinos pueden derivar sus propios protectores individuales en función de sus propias necesidades, pero independientemente de lo difícil que intenten no crear protectores que entren en conflicto con cualquier otro inquilino del sistema. Gráficamente, esto se representa como se indica a continuación.

![Propósitos de varios inquilinos](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Se supone que la aplicación de paraguas controla qué API están disponibles para los inquilinos individuales y que los inquilinos no pueden ejecutar código arbitrario en el servidor. Si un inquilino puede ejecutar código arbitrario, podría realizar la reflexión privada para interrumpir las garantías de aislamiento o simplemente leer el material de creación de claves maestro directamente y derivar las subclaves que deseen.

En realidad, el sistema de protección de datos usa una especie de varios inquilinos en su configuración predeterminada. De forma predeterminada, el material de creación de claves maestra se almacena en la carpeta de Perfil de usuario de la cuenta de proceso de trabajo (o el registro para las identidades del grupo de aplicaciones de IIS). Pero es realmente frecuente usar una única cuenta para ejecutar varias aplicaciones y, por lo tanto, todas estas aplicaciones terminaran compartiendo el material de creación de claves maestra. Para solucionarlo, el sistema de protección de datos inserta automáticamente un identificador único por aplicación como el primer elemento en la cadena de propósito general. Este propósito implícito sirve para [aislar las aplicaciones individuales](xref:security/data-protection/configuration/overview#per-application-isolation) entre sí al tratar eficazmente cada aplicación como un inquilino único dentro del sistema y el proceso de creación del protector es idéntico a la imagen anterior.
