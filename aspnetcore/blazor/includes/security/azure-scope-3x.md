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
ms.openlocfilehash: ae5d8cf64c0db9402a5457cec1df8402f4db103c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551833"
---
<span data-ttu-id="e5a26-101">Azure Portal proporciona uno de los siguientes formatos de URI de identificador de aplicación en función de si el inquilino de AAD tiene un [dominio de publicador comprobado o no comprobado](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="e5a26-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="e5a26-102">Cuando se usa el último de los dos URI de identificador de aplicación anteriores en la aplicación cliente para formar el URI de ámbito y la autorización no se realiza correctamente, intente proporcionar el URI de ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="e5a26-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="e5a26-103">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e5a26-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
