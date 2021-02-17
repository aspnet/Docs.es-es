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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552691"
---
## <a name="debug-diagnostics"></a>Diagnóstico de depuración

Para ver la salida detallada del diagnóstico de cálculo de ruta, establezca `Logging:LogLevel:Microsoft` en `Debug`. En el entorno de desarrollo, establezca el nivel de registro en *appsettings.Development.json*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
