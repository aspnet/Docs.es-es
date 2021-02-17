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
> <span data-ttu-id="2e153-101">Cuando se usa <xref:System.Text.RegularExpressions> para procesar entradas que no son de confianza, pase un tiempo de expiración.</span><span class="sxs-lookup"><span data-stu-id="2e153-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="2e153-102">Un usuario malintencionado puede proporcionar entradas a `RegularExpressions` y provocar un [ataque por denegación de servicio](https://www.us-cert.gov/ncas/tips/ST04-015).</span><span class="sxs-lookup"><span data-stu-id="2e153-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="2e153-103">Las API del marco ASP.NET Core en las que se usa `RegularExpressions` pasan un tiempo de expiración.</span><span class="sxs-lookup"><span data-stu-id="2e153-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>