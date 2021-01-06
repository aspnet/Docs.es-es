# <a name="visual-studio"></a>[<span data-ttu-id="2e216-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e216-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2e216-102">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="2e216-102">Create a new project.</span></span>
1. <span data-ttu-id="2e216-103">Seleccione **Worker Service** (Servicio de Worker).</span><span class="sxs-lookup"><span data-stu-id="2e216-103">Select **Worker Service**.</span></span> <span data-ttu-id="2e216-104">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="2e216-104">Select **Next**.</span></span>
1. <span data-ttu-id="2e216-105">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2e216-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2e216-106">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="2e216-106">Select **Create**.</span></span>
1. <span data-ttu-id="2e216-107">En el cuadro de diálogo **Crear un servicio de Worker**, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="2e216-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e216-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2e216-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2e216-109">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="2e216-109">Create a new project.</span></span>
1. <span data-ttu-id="2e216-110">Seleccione **Aplicación** en **.NET Core** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="2e216-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="2e216-111">Seleccione **Trabajador** en **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2e216-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="2e216-112">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="2e216-112">Select **Next**.</span></span>
1. <span data-ttu-id="2e216-113">Seleccione **.NET Core 3.0** o una versión posterior para **Marco de trabajo de destino**.</span><span class="sxs-lookup"><span data-stu-id="2e216-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="2e216-114">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="2e216-114">Select **Next**.</span></span>
1. <span data-ttu-id="2e216-115">Proporcione un nombre en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="2e216-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="2e216-116">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="2e216-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2e216-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="2e216-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2e216-118">Desde un shell de comandos, use la plantilla Worker Service (`worker`) con el comando [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="2e216-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="2e216-119">En el ejemplo siguiente, se crea una aplicación Worker Service llamada `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="2e216-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="2e216-120">Al ejecutar el comando, se crea automáticamente una carpeta para la aplicación `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="2e216-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
