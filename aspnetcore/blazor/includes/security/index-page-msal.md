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
<span data-ttu-id="f4cb1-101">La página de índice (`wwwroot/index.html`) incluye un script que define `AuthenticationService` en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4cb1-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="f4cb1-102">`AuthenticationService` controla los detalles de bajo nivel del protocolo OIDC.</span><span class="sxs-lookup"><span data-stu-id="f4cb1-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="f4cb1-103">La aplicación llama internamente a métodos definidos en el script para realizar las operaciones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f4cb1-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
