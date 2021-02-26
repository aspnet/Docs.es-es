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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552238"
---
<span data-ttu-id="63225-101">El componente `RedirectToLogin` (`Shared/RedirectToLogin.razor`):</span><span class="sxs-lookup"><span data-stu-id="63225-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="63225-102">Administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="63225-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="63225-103">Conserva la dirección URL actual a la que el usuario intenta tener acceso para que pueda volver a esa página si la autenticación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="63225-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
