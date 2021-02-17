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
<span data-ttu-id="fb319-101">Ejecute el Identity scaffolding:</span><span class="sxs-lookup"><span data-stu-id="fb319-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb319-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb319-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fb319-103">En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="fb319-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="fb319-104">En el panel izquierdo del cuadro de diálogo **Agregar nuevo elemento con scaffolding** , seleccione **Identity**  >  **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="fb319-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="fb319-105">En el cuadro de diálogo **agregar Identity** , seleccione las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="fb319-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="fb319-106">Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto:</span><span class="sxs-lookup"><span data-stu-id="fb319-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="fb319-107">`~/Pages/Shared/_Layout.cshtml` para Razor páginas</span><span class="sxs-lookup"><span data-stu-id="fb319-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="fb319-108">`~/Views/Shared/_Layout.cshtml` para proyectos de MVC</span><span class="sxs-lookup"><span data-stu-id="fb319-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="fb319-109">Blazor Server de forma predeterminada, las aplicaciones creadas a partir de la Blazor Server plantilla ( `blazorserver` ) no están configuradas para Razor páginas o MVC.</span><span class="sxs-lookup"><span data-stu-id="fb319-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="fb319-110">Deje la entrada página de diseño en blanco.</span><span class="sxs-lookup"><span data-stu-id="fb319-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="fb319-111">Seleccione el **+** botón para crear una nueva **clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="fb319-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="fb319-112">Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="fb319-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="fb319-113">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="fb319-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fb319-114">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb319-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="fb319-115">Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:</span><span class="sxs-lookup"><span data-stu-id="fb319-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="fb319-116">Agregue las referencias de paquete de NuGet necesarias al archivo de proyecto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="fb319-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="fb319-117">Ejecute los siguientes comandos en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="fb319-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="fb319-118">Ejecute el siguiente comando para enumerar las Identity Opciones del scaffolding:</span><span class="sxs-lookup"><span data-stu-id="fb319-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="fb319-119">En la carpeta del proyecto, ejecute el Identity scaffolding con las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="fb319-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="fb319-120">Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="fb319-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
