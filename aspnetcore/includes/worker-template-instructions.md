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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552958"
---
# <a name="visual-studio"></a>[<span data-ttu-id="a5aa9-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5aa9-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a5aa9-102">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-102">Create a new project.</span></span>
1. <span data-ttu-id="a5aa9-103">Seleccione **Worker Service** (Servicio de Worker).</span><span class="sxs-lookup"><span data-stu-id="a5aa9-103">Select **Worker Service**.</span></span> <span data-ttu-id="a5aa9-104">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-104">Select **Next**.</span></span>
1. <span data-ttu-id="a5aa9-105">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a5aa9-106">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-106">Select **Create**.</span></span>
1. <span data-ttu-id="a5aa9-107">En el cuadro de diálogo **Crear un servicio de Worker**, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5aa9-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a5aa9-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a5aa9-109">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-109">Create a new project.</span></span>
1. <span data-ttu-id="a5aa9-110">Seleccione **Aplicación** en **.NET Core** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="a5aa9-111">Seleccione **Trabajador** en **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="a5aa9-112">Seleccione **Next** (Siguiente).</span><span class="sxs-lookup"><span data-stu-id="a5aa9-112">Select **Next**.</span></span>
1. <span data-ttu-id="a5aa9-113">Seleccione **.NET Core 3.0** o una versión posterior para **Marco de trabajo de destino**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="a5aa9-114">Seleccione **Next** (Siguiente).</span><span class="sxs-lookup"><span data-stu-id="a5aa9-114">Select **Next**.</span></span>
1. <span data-ttu-id="a5aa9-115">Proporcione un nombre en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="a5aa9-116">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a5aa9-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5aa9-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a5aa9-118">Desde un shell de comandos, use la plantilla Worker Service (`worker`) con el comando [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="a5aa9-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="a5aa9-119">En el ejemplo siguiente, se crea una aplicación Worker Service llamada `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="a5aa9-120">Al ejecutar el comando, se crea automáticamente una carpeta para la aplicación `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="a5aa9-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
