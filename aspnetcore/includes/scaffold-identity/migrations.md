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
<span data-ttu-id="5244f-101">El Identity código de base de datos generado requiere migraciones de [Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="5244f-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="5244f-102">Cree una migración y actualice la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5244f-102">Create a migration and update the database.</span></span> <span data-ttu-id="5244f-103">Por ejemplo, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="5244f-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5244f-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5244f-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5244f-105">En la consola del **Administrador de paquetes** de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5244f-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5244f-106">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="5244f-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="5244f-107">El Identity parámetro de nombre "Create Schema" del `Add-Migration` comando es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="5244f-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="5244f-108">`"CreateIdentitySchema"` describe la migración.</span><span class="sxs-lookup"><span data-stu-id="5244f-108">`"CreateIdentitySchema"` describes the migration.</span></span>
