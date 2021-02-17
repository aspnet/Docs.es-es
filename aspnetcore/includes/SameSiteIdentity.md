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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552123"
---
[Identity](xref:security/authentication/identity)En gran medida, ASP.net Core no se ve afectado por [SameSite cookie s](xref:security/samesite) excepto en escenarios avanzados como `IFrames` o `OpenIdConnect` integración.

Al usar `Identity` , ***no*** agregue ningún cookie proveedor ni llame a ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` , se `Identity` encarga de ello.