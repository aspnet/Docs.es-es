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
ms.openlocfilehash: ce0a993093812c9a477d85d363827988ae9bd536
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551918"
---
La página de índice (`wwwroot/index.html`) incluye un script que define `AuthenticationService` en JavaScript. `AuthenticationService` controla los detalles de bajo nivel del protocolo OIDC. La aplicación llama internamente a métodos definidos en el script para realizar las operaciones de autenticación.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
