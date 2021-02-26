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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552451"
---
<span data-ttu-id="771fe-101">El marco tiene como valor predeterminado el modo de inicio de sesión emergente y vuelve al modo de inicio de sesión de redireccionamiento si no se puede abrir un elemento emergente.</span><span class="sxs-lookup"><span data-stu-id="771fe-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="771fe-102">Configure MSAL para usar el modo de inicio de sesión de redireccionamiento mediante el establecimiento de la propiedad `LoginMode` de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> en `redirect`:</span><span class="sxs-lookup"><span data-stu-id="771fe-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="771fe-103">La configuración predeterminada es `popup`y el valor de cadena no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="771fe-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
