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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552577"
---
Ejecute el Identity scaffolding:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding**.
* En el panel izquierdo del cuadro de diálogo **Agregar nuevo elemento con scaffolding** , seleccione **Identity**  >  **Agregar**.
* En el cuadro de diálogo **agregar Identity** , seleccione las opciones que desee.
  * Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto:
    * `~/Pages/Shared/_Layout.cshtml` para Razor páginas
    * `~/Views/Shared/_Layout.cshtml` para proyectos de MVC
    * Blazor Server de forma predeterminada, las aplicaciones creadas a partir de la Blazor Server plantilla ( `blazorserver` ) no están configuradas para Razor páginas o MVC. Deje la entrada página de diseño en blanco.
  * Seleccione el **+** botón para crear una nueva **clase de contexto de datos**. Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).
* Seleccione **Agregar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Agregue las referencias de paquete de NuGet necesarias al archivo de proyecto (*. csproj*). Ejecute los siguientes comandos en el directorio del proyecto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Ejecute el siguiente comando para enumerar las Identity Opciones del scaffolding:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

En la carpeta del proyecto, ejecute el Identity scaffolding con las opciones que desee. Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
