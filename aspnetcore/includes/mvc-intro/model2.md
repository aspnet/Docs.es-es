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
ms.openlocfilehash: 44cef0c1491cc609dd9b821910014ec88ecaad81
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551290"
---
::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Cree una carpeta *Data*.

Agregue la clase `MvcMovieContext` siguiente a la carpeta *Data*:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al *appsettings.json* :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Agregue los paquetes NuGet y las herramientas EF.

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Compile el proyecto para comprobar si hay errores del compilador.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Agregue la clase `MvcMovieContext` siguiente a la carpeta *Models*:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al *appsettings.json* :

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Adición de los paquetes NuGet necesarios

Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile el proyecto para comprobar si hay errores.
::: moniker-end