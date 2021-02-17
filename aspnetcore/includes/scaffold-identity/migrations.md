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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551858"
---
El Identity código de base de datos generado requiere migraciones de [Entity Framework Core](/ef/core/managing-schemas/migrations/). Cree una migración y actualice la base de datos. Por ejemplo, ejecute los siguientes comandos:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del **Administrador de paquetes** de Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

El Identity parámetro de nombre "Create Schema" del `Add-Migration` comando es arbitrario. `"CreateIdentitySchema"` describe la migración.
