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
ms.openlocfilehash: cb02af885536fb64cfe33051b6939823a6f3be46
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552090"
---
El espacio de nombres <xref:Microsoft.AspNetCore.Components.Authorization?displayProperty=fullName> está disponible en toda la aplicación a través del archivo `_Imports.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](imports-standalone-5x.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](imports-standalone-3x.razor?highlight=3)]

::: moniker-end
