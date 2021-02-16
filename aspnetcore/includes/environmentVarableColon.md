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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536313"
---
<span data-ttu-id="1527d-101">El separador `:` no funciona con claves jerárquicas de variables de entorno en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="1527d-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="1527d-102">`__`, el carácter de subrayado doble, tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="1527d-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="1527d-103">Es compatible con todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="1527d-103">Supported by all platforms.</span></span> <span data-ttu-id="1527d-104">Por ejemplo, el separador `:` no es compatible con [Bash](https://linuxhint.com/bash-environment-variables/), pero `__` sí.</span><span class="sxs-lookup"><span data-stu-id="1527d-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="1527d-105">Se reemplaza automáticamente por un signo `:`.</span><span class="sxs-lookup"><span data-stu-id="1527d-105">Automatically replaced by a `:`</span></span>