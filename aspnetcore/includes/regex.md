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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551654"
---
> [!WARNING]
> Cuando se usa <xref:System.Text.RegularExpressions> para procesar entradas que no son de confianza, pase un tiempo de expiración. Un usuario malintencionado puede proporcionar entradas a `RegularExpressions` y provocar un [ataque por denegación de servicio](https://www.us-cert.gov/ncas/tips/ST04-015). Las API del marco ASP.NET Core en las que se usa `RegularExpressions` pasan un tiempo de expiración.