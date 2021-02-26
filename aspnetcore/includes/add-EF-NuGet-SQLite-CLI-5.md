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
Ejecute los siguientes comandos de la CLI de .NET:

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

Los comandos anteriores agregan:

* La [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Las herramientas de EF Core para la CLI de .NET
* El proveedor SQLite de EF Core, que instala el paquete de EF Core como una dependencia.
* Los paquetes necesarios para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` y `Microsoft.EntityFrameworkCore.SqlServer`.

Para obtener instrucciones sobre la configuración de varios entornos que permite que una aplicación configure sus contextos de base de datos por entorno, vea <xref:fundamentals/environments#environment-based-startup-class-and-methods>.

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
