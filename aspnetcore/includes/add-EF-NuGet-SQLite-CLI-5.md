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
ms.openlocfilehash: 27cb64dc8734fcb6d165795515096c9d9dd2a9cc
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552114"
---
<span data-ttu-id="7d992-101">Ejecute los siguientes comandos de la CLI de .NET:</span><span class="sxs-lookup"><span data-stu-id="7d992-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="7d992-102">Los comandos anteriores agregan:</span><span class="sxs-lookup"><span data-stu-id="7d992-102">The preceding commands add:</span></span>

* <span data-ttu-id="7d992-103">La [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7d992-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="7d992-104">Las herramientas de EF Core para la CLI de .NET</span><span class="sxs-lookup"><span data-stu-id="7d992-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="7d992-105">El proveedor SQLite de EF Core, que instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="7d992-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="7d992-106">Los paquetes necesarios para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` y `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="7d992-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="7d992-107">Para obtener instrucciones sobre la configuración de varios entornos que permite que una aplicación configure sus contextos de base de datos por entorno, vea <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="7d992-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
