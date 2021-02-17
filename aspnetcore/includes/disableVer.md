---
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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551570"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Deshabilitar la comprobación de la cuenta predeterminada

Con las plantillas predeterminadas, se redirige al usuario a, `Account.RegisterConfirmation` donde puede seleccionar un vínculo para que la cuenta se confirme. El valor predeterminado `Account.RegisterConfirmation` ***solo*** se usa para las pruebas; la comprobación automática de cuentas debe estar deshabilitada en una aplicación de producción.

Para requerir una cuenta confirmada y evitar el inicio de sesión inmediato en el registro, establezca `DisplayConfirmAccountLink = false` en */areas/ Identity /pages/Account/RegisterConfirmation.cshtml.CS*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]