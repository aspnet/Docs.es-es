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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551400"
---
Los finalizadores HTTP son similares a los encabezados HTTP, salvo que se envían después de enviar el cuerpo de la respuesta. Para IIS y HTTP.sys, solo se admiten los finalizadores de respuesta HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

En el código de ejemplo anterior:

* `SupportsTrailers` garantiza que los finalizadores se admitan para la respuesta.
* `DeclareTrailer` agrega el nombre del finalizador dado al encabezado de respuesta `Trailer`. Declarar los finalizadores de una respuesta es opcional, pero se recomienda. Si se llama a `DeclareTrailer`, debe ser antes de que se envíen los encabezados de respuesta.
* `AppendTrailer` anexa el finalizador.
