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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552555"
---
> [!NOTE]
> Antes de ASP.NET Core 3.0, las aplicaciones escribían un registro de tipo `LogLevel.Warning` por solicitud si no se admitía la referencia cultural solicitada. El registro de un valor `LogLevel.Warning` por solicitud puede crear archivos de registro de gran tamaño con información redundante. Este comportamiento ha cambiado en ASP.NET 3.0. `RequestLocalizationMiddleware` escribe un registro de tipo `LogLevel.Debug`, lo que reduce el tamaño de los registros de producción.
