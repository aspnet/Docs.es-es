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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551330"
---
Un restablecimiento permite que el servidor restablezca una solicitud HTTP/2 con un código de error especificado. Una solicitud de restablecimiento se considera una anulación.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

En el ejemplo de código anterior, `Reset` especifica el código de error de `INTERNAL_ERROR`. Para obtener más información sobre los códigos de error HTTP/2, visite la [sección de código de error de especificación de HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).