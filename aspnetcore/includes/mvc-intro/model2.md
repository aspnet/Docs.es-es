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

<span data-ttu-id="bf25b-101">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="bf25b-101">Create a *Data* folder.</span></span>

<span data-ttu-id="bf25b-102">Agregue la clase `MvcMovieContext` siguiente a la carpeta *Data*:</span><span class="sxs-lookup"><span data-stu-id="bf25b-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="bf25b-103">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf25b-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="bf25b-104">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf25b-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="bf25b-105">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf25b-105">Add a database connection string</span></span>

<span data-ttu-id="bf25b-106">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="bf25b-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="bf25b-107">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="bf25b-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="bf25b-108">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf25b-108">Register the database context</span></span>

<span data-ttu-id="bf25b-109">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bf25b-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="bf25b-110">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf25b-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="bf25b-111">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="bf25b-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf25b-112">Agregue la clase `MvcMovieContext` siguiente a la carpeta *Models*:</span><span class="sxs-lookup"><span data-stu-id="bf25b-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="bf25b-113">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf25b-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="bf25b-114">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf25b-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="bf25b-115">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf25b-115">Add a database connection string</span></span>

<span data-ttu-id="bf25b-116">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="bf25b-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="bf25b-117">Adición de los paquetes NuGet necesarios</span><span class="sxs-lookup"><span data-stu-id="bf25b-117">Add required NuGet packages</span></span>

<span data-ttu-id="bf25b-118">Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:</span><span class="sxs-lookup"><span data-stu-id="bf25b-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="bf25b-119">El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bf25b-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="bf25b-120">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf25b-120">Register the database context</span></span>

<span data-ttu-id="bf25b-121">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bf25b-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="bf25b-122">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf25b-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="bf25b-123">Compile el proyecto para comprobar si hay errores.</span><span class="sxs-lookup"><span data-stu-id="bf25b-123">Build the project as a check for errors.</span></span>
::: moniker-end