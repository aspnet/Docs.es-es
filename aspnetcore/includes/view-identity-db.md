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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551660"
---
### <a name="view-the-identity-database"></a>Ver la Identity base de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* En el menú **Ver** , seleccione **Explorador de objetos de SQL Server** (SSOX).
* Vaya a **(LocalDB) MSSQLLocalDB (SQL Server 13)**. Haga clic con el botón secundario en **dbo. AspNetUsers**  >  **ver datos**:

![Menú contextual en la tabla AspNetUsers de Explorador de objetos de SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Hay muchas herramientas de terceros que puede descargar para administrar y ver una base de datos de SQLite, por ejemplo, el [Explorador de base de datos de SQLite](https://sqlitebrowser.org/).

---