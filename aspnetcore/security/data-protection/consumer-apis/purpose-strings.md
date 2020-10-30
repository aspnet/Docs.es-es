---
title: Cadenas de propósito en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se usan las cadenas de propósito en las API de protección de datos de ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060812"
---
# <a name="purpose-strings-in-aspnet-core"></a>Cadenas de propósito en ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Los componentes que consumen `IDataProtectionProvider` deben pasar un parámetro de *propósitos* únicos al `CreateProtector` método. El *parámetro* propósitos es inherente a la seguridad del sistema de protección de datos, ya que proporciona aislamiento entre los consumidores criptográficos, incluso si las claves criptográficas raíz son las mismas.

Cuando un consumidor especifica un propósito, se usa la cadena de propósito junto con las claves criptográficas raíz para derivar las subclaves criptográficas únicas de ese consumidor. Esto aísla al consumidor de todos los demás consumidores criptográficos de la aplicación: ningún otro componente puede leer sus cargas y no puede leer las cargas de cualquier otro componente. Este aislamiento también representa todas las categorías de ataque inviables contra el componente.

![Ejemplo de diagrama de propósito](purpose-strings/_static/purposes.png)

En el diagrama anterior, `IDataProtector` las instancias A y B **no pueden** leer las cargas de los demás, solo las suyas propias.

La cadena de propósito no tiene que ser secreta. Simplemente debe ser único en el sentido de que ningún otro componente con el comportamiento correcto deberá proporcionar la misma cadena de propósito.

>[!TIP]
> Usar el espacio de nombres y el nombre de tipo del componente que consume las API de protección de datos es una buena regla general, como en la práctica, esta información nunca entrará en conflicto.
>
>Un componente creado por contoso que sea responsable de la creación de tokens de portador puede usar contoso. Security. BearerToken como su cadena de propósito. O incluso mejor: podría usar contoso. Security. BearerToken. v1 como su cadena de propósito. Anexar el número de versión permite que una versión futura use contoso. Security. BearerToken. V2 como su finalidad y las distintas versiones estarán completamente aisladas unas de otras en lo que respecta a las cargas.

Dado que el parámetro propósitos de `CreateProtector` es una matriz de cadena, en su lugar se podría especificar como `[ "Contoso.Security.BearerToken", "v1" ]` . Esto permite establecer una jerarquía de propósitos y abre la posibilidad de escenarios de varios inquilinos con el sistema de protección de datos.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Los componentes no deben permitir que los datos proporcionados por el usuario que no son de confianza sean el único origen de entrada de la cadena de propósitos.
>
>Por ejemplo, considere un componente contoso. Messaging. SecureMessage, que es responsable de almacenar los mensajes seguros. Si el componente de mensajería segura tuviera que llamar a `CreateProtector([ username ])` , un usuario malintencionado podría crear una cuenta con el nombre de usuario "contoso. Security. BearerToken" en un intento de obtener el componente al que llamar `CreateProtector([ "Contoso.Security.BearerToken" ])` , lo que provocaría accidentalmente que el sistema de mensajería seguro pudiera ver las cargas que podrían considerarse como tokens de autenticación.
>
>Una cadena de propósitos mejor para el componente de mensajería sería `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , lo que proporciona un aislamiento adecuado.

El aislamiento proporcionado por y los comportamientos de `IDataProtectionProvider` , `IDataProtector` y son los siguientes:

* Para un `IDataProtectionProvider` objeto determinado, el `CreateProtector` método creará un `IDataProtector` objeto vinculado de forma única al `IDataProtectionProvider` objeto que lo creó y al parámetro propósitos que se pasó al método.

* El parámetro purpose no debe ser null. (Si se especifica propósitos como una matriz, esto significa que la matriz no debe tener una longitud de cero y que todos los elementos de la matriz no deben ser null). Técnicamente, se permite un propósito de cadena vacía, pero no se recomienda.

* Los argumentos de dos propósitos son equivalentes si y solo si contienen las mismas cadenas (mediante un comparador ordinal) en el mismo orden. Un único argumento de propósito es equivalente a la matriz de propósitos de un solo elemento correspondiente.

* Dos `IDataProtector` objetos son equivalentes si y solo si se crean a partir de objetos equivalentes `IDataProtectionProvider` con los mismos parámetros de propósitos equivalentes.

* Para un `IDataProtector` objeto determinado, una llamada a `Unprotect(protectedData)` devolverá el original `unprotectedData` si y solo si `protectedData := Protect(unprotectedData)` para un `IDataProtector` objeto equivalente.

> [!NOTE]
> No estamos pensando en el caso de que algún componente elija intencionadamente una cadena de propósito que se sabe que entra en conflicto con otro componente. Este componente se consideraría esencialmente malintencionado y este sistema no está diseñado para proporcionar garantías de seguridad en caso de que el código malintencionado ya se esté ejecutando dentro del proceso de trabajo.
