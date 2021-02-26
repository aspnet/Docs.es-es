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
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552794"
---
Cuando se trabaja con una API de servidor registrada en AAD y el registro de AAD de la aplicación se encuentra en un inquilino que se basa en un [dominio de publicador no comprobado](/azure/active-directory/develop/howto-configure-publisher-domain), el URI del identificador de la aplicación de la API del servidor no es `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` sino que, en su lugar, tiene el formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`. Si ese es el caso, el ámbito del token de acceso predeterminado en `Program.Main` (`Program.cs`) de la aplicación *`Client`* es similar al siguiente:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Para configurar la aplicación de API de servidor para una audiencia coincidente, establezca el valor `Audience` en el archivo de configuración de la aplicación de API *`Server`* (`appsettings.json`) para que coincida con la audiencia de la aplicación proporcionada por Azure Portal:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

En la configuración anterior, el final del valor `Audience` **no** incluye el ámbito predeterminado `/{DEFAULT SCOPE}`.

Ejemplo:

`Program.Main` (`Program.cs`) de la aplicación *`Client`* :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Configure el archivo de configuración de la aplicación de API *`Server`* (`appsettings.json`) con una audiencia correspondiente (`Audience`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

En el ejemplo anterior, el final del valor `Audience` **no** incluye el ámbito predeterminado `/API.Access`.
