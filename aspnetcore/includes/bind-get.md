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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552315"
---
> [!WARNING]
> Por motivos de seguridad, debe participar en el enlace de datos de solicitud `GET` con las propiedades del modelo de página. Compruebe las entradas de los usuarios antes de asignarlas a las propiedades. Si participa en el enlace de `GET`, le puede ser útil al trabajar con escenarios que dependan de cadenas de consultas o valores de rutas.
>
> Para enlazar una propiedad en solicitudes `GET`, establezca la propiedad `SupportsGet` del atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) en `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Para obtener más información, consulte [Reunión de la comunidad de ASP.NET: debate sobre enlazar en GET (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
