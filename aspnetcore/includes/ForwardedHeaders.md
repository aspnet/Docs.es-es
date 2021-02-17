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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551526"
---
El middleware de encabezados reenviados debe ejecutarse antes de otro middleware. Hacerlo en ese orden garantiza que el middleware que se basa en la información de encabezados reenviados pueda usar los valores de encabezado para procesarlos. Para ejecutar el middleware de encabezados reenviados después del middleware de diagnóstico y control de errores, vea [Orden del middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#fhmo).