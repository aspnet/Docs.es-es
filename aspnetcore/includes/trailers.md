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
<span data-ttu-id="70fc3-101">Los finalizadores HTTP son similares a los encabezados HTTP, salvo que se envían después de enviar el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="70fc3-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="70fc3-102">Para IIS y HTTP.sys, solo se admiten los finalizadores de respuesta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="70fc3-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="70fc3-103">En el código de ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="70fc3-103">In the preceding example code:</span></span>

* <span data-ttu-id="70fc3-104">`SupportsTrailers` garantiza que los finalizadores se admitan para la respuesta.</span><span class="sxs-lookup"><span data-stu-id="70fc3-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="70fc3-105">`DeclareTrailer` agrega el nombre del finalizador dado al encabezado de respuesta `Trailer`.</span><span class="sxs-lookup"><span data-stu-id="70fc3-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="70fc3-106">Declarar los finalizadores de una respuesta es opcional, pero se recomienda.</span><span class="sxs-lookup"><span data-stu-id="70fc3-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="70fc3-107">Si se llama a `DeclareTrailer`, debe ser antes de que se envíen los encabezados de respuesta.</span><span class="sxs-lookup"><span data-stu-id="70fc3-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="70fc3-108">`AppendTrailer` anexa el finalizador.</span><span class="sxs-lookup"><span data-stu-id="70fc3-108">`AppendTrailer` appends the trailer.</span></span>
