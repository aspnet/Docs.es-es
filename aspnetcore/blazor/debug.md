---
title: 'Depuración de :::no-loc(Blazor WebAssembly)::: en ASP.NET Core'
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 669ebaf6dcd05561340aefda4a75b6fe1068d207
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056197"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="9a980-103">Depuración de :::no-loc(Blazor WebAssembly)::: en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a980-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

[<span data-ttu-id="9a980-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="9a980-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="9a980-105">Las aplicaciones :::no-loc(Blazor WebAssembly)::: se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="9a980-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="9a980-106">También puede depurar la aplicación con los siguientes entornos de desarrollo integrado (IDE):</span><span class="sxs-lookup"><span data-stu-id="9a980-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="9a980-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a980-107">Visual Studio</span></span>
* <span data-ttu-id="9a980-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a980-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="9a980-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a980-109">Visual Studio Code</span></span>

<span data-ttu-id="9a980-110">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a980-110">Available scenarios include:</span></span>

* <span data-ttu-id="9a980-111">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9a980-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="9a980-112">Ejecute la aplicación con compatibilidad de depuración en IDE.</span><span class="sxs-lookup"><span data-stu-id="9a980-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="9a980-113">Examine el código en un solo paso.</span><span class="sxs-lookup"><span data-stu-id="9a980-113">Single-step through the code.</span></span>
* <span data-ttu-id="9a980-114">Reanude la ejecución de código con un método abreviado de teclado en IDE.</span><span class="sxs-lookup"><span data-stu-id="9a980-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="9a980-115">En la ventana *Variables locales* , observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="9a980-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="9a980-116">Vea la pila de llamadas, incluidas las cadenas de llamadas entre JavaScript y .NET.</span><span class="sxs-lookup"><span data-stu-id="9a980-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="9a980-117">Por ahora, *no puede* :</span><span class="sxs-lookup"><span data-stu-id="9a980-117">For now, you *can't* :</span></span>

* <span data-ttu-id="9a980-118">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="9a980-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="9a980-119">Alcanzar puntos de interrupción durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9a980-120">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9a980-121">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9a980-121">Prerequisites</span></span>

<span data-ttu-id="9a980-122">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a980-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="9a980-123">Google Chrome (versión 70 o posterior) (predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9a980-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="9a980-124">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="9a980-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="9a980-125">Visual Studio para Mac requiere la versión 8.8 (compilación 1532) o una posterior:</span><span class="sxs-lookup"><span data-stu-id="9a980-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="9a980-126">Instale la versión más reciente de Visual Studio para Mac seleccionando el botón **Descargar Visual Studio para Mac** en [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="9a980-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="9a980-127">Seleccione el canal *Vista preliminar* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9a980-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="9a980-128">Para obtener más información, vea [Instalación de la versión más reciente de Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="9a980-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="9a980-129">Apple Safari en macOS no se admite actualmente.</span><span class="sxs-lookup"><span data-stu-id="9a980-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="9a980-130">Habilitar la depuración</span><span class="sxs-lookup"><span data-stu-id="9a980-130">Enable debugging</span></span>

<span data-ttu-id="9a980-131">Para habilitar la depuración de una aplicación :::no-loc(Blazor WebAssembly):::, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="9a980-131">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="9a980-132">Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9a980-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="9a980-133">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="9a980-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="9a980-134">Permite que el IDE detecte que la aplicación es una aplicación :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="9a980-134">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="9a980-135">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="9a980-135">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="9a980-136">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="9a980-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a980-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a980-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a980-138">Para depurar una aplicación :::no-loc(Blazor WebAssembly)::: en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9a980-138">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="9a980-139">Cree una aplicación hospedada :::no-loc(Blazor WebAssembly)::: de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a980-139">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="9a980-140">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="9a980-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9a980-141">No admite **Iniciar sin depuración** ( <kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a980-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9a980-142">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9a980-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9a980-143">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9a980-143">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="9a980-144">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9a980-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="9a980-145">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="9a980-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="9a980-146">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="9a980-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="9a980-147">Al depurar una aplicación de :::no-loc(Blazor WebAssembly):::, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="9a980-147">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="9a980-148">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="9a980-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9a980-149">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="9a980-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9a980-150">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="9a980-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="9a980-151">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="9a980-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="9a980-152">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9a980-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="9a980-153">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9a980-154">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="9a980-155">Si la aplicación se hospeda en una [ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path) distinta de `/`, actualice las propiedades siguientes en `Properties/launchSettings.json` para reflejar la ruta de acceso base de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="9a980-155">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="9a980-156">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="9a980-156">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="9a980-157">`inspectUri` de cada perfil:</span><span class="sxs-lookup"><span data-stu-id="9a980-157">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="9a980-158">Los marcadores de posición en la configuración anterior:</span><span class="sxs-lookup"><span data-stu-id="9a980-158">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="9a980-159">`{INSECURE PORT}`: puerto no seguro.</span><span class="sxs-lookup"><span data-stu-id="9a980-159">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="9a980-160">De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.</span><span class="sxs-lookup"><span data-stu-id="9a980-160">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="9a980-161">`{APP BASE PATH}`: ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-161">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="9a980-162">`{SECURE PORT}`: puerto seguro.</span><span class="sxs-lookup"><span data-stu-id="9a980-162">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="9a980-163">De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.</span><span class="sxs-lookup"><span data-stu-id="9a980-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="9a980-164">`{PROFILE 1, 2, ... N}`: perfiles de configuración de inicio.</span><span class="sxs-lookup"><span data-stu-id="9a980-164">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="9a980-165">Normalmente, una aplicación especifica más de un perfil de forma predeterminada (por ejemplo, un perfil para IIS Express y un perfil de proyecto, que se usa en el servidor de Kestrel).</span><span class="sxs-lookup"><span data-stu-id="9a980-165">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="9a980-166">En los ejemplos siguientes, la aplicación se hospeda en `/OAT` con una ruta de acceso base de la aplicación configurada en `wwwroot/index.html` como `<base href="/OAT/">`:</span><span class="sxs-lookup"><span data-stu-id="9a980-166">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="9a980-167">Para obtener información sobre el uso de una ruta de acceso base de la aplicación personalizada para las aplicaciones de :::no-loc(Blazor WebAssembly):::, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9a980-167">For information on using a custom app base path for :::no-loc(Blazor WebAssembly)::: apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a980-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a980-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="9a980-169">Depuración de una aplicación independiente :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="9a980-169">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span></h2>

1. <span data-ttu-id="9a980-170">Abra la aplicación independiente :::no-loc(Blazor WebAssembly)::: en VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a980-170">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="9a980-171">Puede recibir una notificación que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="9a980-171">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="9a980-172">Se necesita una configuración adicional para depurar aplicaciones :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="9a980-172">Additional setup is required to debug :::no-loc(Blazor WebAssembly)::: applications.</span></span>

   <span data-ttu-id="9a980-173">Si recibe la notificación:</span><span class="sxs-lookup"><span data-stu-id="9a980-173">If you receive the notification:</span></span>

   * <span data-ttu-id="9a980-174">Confirme que está instalada la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="9a980-174">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="9a980-175">Para inspeccionar las extensiones instaladas, abra **Ver** > **Extensiones** en la barra de menús o seleccione el icono **Extensiones** en la barra lateral **Actividad**.</span><span class="sxs-lookup"><span data-stu-id="9a980-175">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="9a980-176">Confirme que está habilitada la depuración de JavaScript en versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="9a980-176">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="9a980-177">Abra la configuración en la barra de menús ( **Archivo** > **Preferencias** > **Configuración** ).</span><span class="sxs-lookup"><span data-stu-id="9a980-177">Open the settings from the menu bar ( **File** > **Preferences** > **Settings** ).</span></span> <span data-ttu-id="9a980-178">Busque con las palabras clave `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="9a980-178">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="9a980-179">En los resultados de la búsqueda, confirme que la casilla **Depurar > JavaScript: Usar vista previa** está activada.</span><span class="sxs-lookup"><span data-stu-id="9a980-179">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="9a980-180">Si la opción para habilitar la depuración de vista previa no está presente, actualice a la versión más reciente de VS Code o instale la [extensión del depurador de JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code 1.46 o versiones anteriores).</span><span class="sxs-lookup"><span data-stu-id="9a980-180">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="9a980-181">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="9a980-181">Reload the window.</span></span>

1. <span data-ttu-id="9a980-182">Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.</span><span class="sxs-lookup"><span data-stu-id="9a980-182">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9a980-183">No admite **Iniciar sin depuración** ( <kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a980-183">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9a980-184">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9a980-184">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9a980-185">Cuando se le pida, seleccione la opción **:::no-loc(Blazor WebAssembly)::: Debug** (Depuración de :::no-loc(Blazor WebAssembly):::) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-185">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

1. <span data-ttu-id="9a980-186">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-186">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="9a980-187">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9a980-187">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="9a980-188">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9a980-188">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="9a980-189">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-189">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9a980-190">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-190">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="9a980-191">Depuración de una aplicación hospedada :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="9a980-191">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="9a980-192">Abra la carpeta de la solución de la aplicación :::no-loc(Blazor WebAssembly)::: hospedada en VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a980-192">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="9a980-193">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a980-193">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="9a980-194">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="9a980-194">Select **Yes**.</span></span>

   > <span data-ttu-id="9a980-195">Faltan los recursos necesarios para compilar y depurar desde "{NOMBRE DE LA APLICACIÓN}".</span><span class="sxs-lookup"><span data-stu-id="9a980-195">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="9a980-196">Add them?"</span><span class="sxs-lookup"><span data-stu-id="9a980-196">Add them?</span></span>

1. <span data-ttu-id="9a980-197">En la paleta de comandos de la parte superior de la ventana, seleccione el proyecto *Server* en la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="9a980-197">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="9a980-198">Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="9a980-198">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="9a980-199">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="9a980-199">Attach to an existing debugging session</span></span>

<span data-ttu-id="9a980-200">Para adjuntar a una aplicación :::no-loc(Blazor)::: en ejecución, cree un archivo `launch.json` con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a980-200">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="9a980-201">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="9a980-201">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="9a980-202">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="9a980-202">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="9a980-203">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="9a980-203">Launch configuration options</span></span>

<span data-ttu-id="9a980-204">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm` (`.vscode/launch.json`).</span><span class="sxs-lookup"><span data-stu-id="9a980-204">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="9a980-205">Opción</span><span class="sxs-lookup"><span data-stu-id="9a980-205">Option</span></span>    | <span data-ttu-id="9a980-206">Descripción</span><span class="sxs-lookup"><span data-stu-id="9a980-206">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="9a980-207">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación :::no-loc(Blazor WebAssembly)::: o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="9a980-207">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="9a980-208">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-208">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="9a980-209">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a980-209">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="9a980-210">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-210">The browser to launch for the debugging session.</span></span> <span data-ttu-id="9a980-211">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a980-211">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="9a980-212">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a980-212">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="9a980-213">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="9a980-213">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="9a980-214">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="9a980-214">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="9a980-215">Se debe establecer en `true` si se inicia y depura una aplicación hospedada :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="9a980-215">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="9a980-216">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="9a980-216">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="9a980-217">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="9a980-217">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="9a980-218">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-218">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="9a980-219">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="9a980-219">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="9a980-220">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="9a980-220">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="9a980-221">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="9a980-221">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="9a980-222">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-222">The working directory to launch the app under.</span></span> <span data-ttu-id="9a980-223">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="9a980-223">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="9a980-224">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="9a980-224">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="9a980-225">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="9a980-225">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="9a980-226">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="9a980-226">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="9a980-227">Inicio y depuración de una aplicación independiente :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="9a980-227">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="9a980-228">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="9a980-228">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="9a980-229">Inicio y depuración de una aplicación :::no-loc(Blazor WebAssembly)::: hospedada con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9a980-229">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="9a980-230">La configuración predeterminada del explorador es Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="9a980-230">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="9a980-231">Al usar Microsoft Edge para la depuración, establezca `browser` en `edge`.</span><span class="sxs-lookup"><span data-stu-id="9a980-231">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="9a980-232">Para usar Google Chrome, no establezca la opción `browser` ni establezca el valor de la opción en `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9a980-232">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="9a980-233">En el ejemplo anterior, `MyHostedApp.Server.dll` es el ensamblado de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="9a980-233">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="9a980-234">La carpeta `.vscode` se encuentra en la carpeta de la solución junto a las carpetas `Client`, `Server` y `Shared`.</span><span class="sxs-lookup"><span data-stu-id="9a980-234">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a980-235">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a980-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9a980-236">Para depurar una aplicación :::no-loc(Blazor WebAssembly)::: en Visual Studio para Mac, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a980-236">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="9a980-237">Cree una aplicación hospedada :::no-loc(Blazor WebAssembly)::: de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a980-237">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="9a980-238">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="9a980-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9a980-239">No se admite la opción **Iniciar sin depurar** ( <kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9a980-239">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="9a980-240">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9a980-240">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="9a980-241">El explorador seleccionado para la sesión de depuración debe ser Google Chrome o Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="9a980-241">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="9a980-242">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9a980-242">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="9a980-243">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="9a980-243">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="9a980-244">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="9a980-244">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="9a980-245">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="9a980-245">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="9a980-246">Al depurar una aplicación de :::no-loc(Blazor WebAssembly):::, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="9a980-246">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="9a980-247">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="9a980-247">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9a980-248">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="9a980-248">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9a980-249">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="9a980-249">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="9a980-250">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="9a980-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="9a980-251">Vuelva a presionar <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9a980-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="9a980-252">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9a980-252">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9a980-253">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-253">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="9a980-254">Para obtener más información, vea [Depuración con Visual Studio para Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="9a980-254">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="9a980-255">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="9a980-255">Debug in the browser</span></span>

<span data-ttu-id="9a980-256">*La guía de esta sección se aplica a Google Chrome y Microsoft Edge en Windows.*</span><span class="sxs-lookup"><span data-stu-id="9a980-256">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="9a980-257">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9a980-257">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="9a980-258">Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="9a980-258">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="9a980-259">En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="9a980-259">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="9a980-260">El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9a980-260">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="9a980-261">Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto.</span><span class="sxs-lookup"><span data-stu-id="9a980-261">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="9a980-262">Siga las instrucciones del explorador, que abre una nueva ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="9a980-262">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="9a980-263">Cierre la ventana anterior del explorador.</span><span class="sxs-lookup"><span data-stu-id="9a980-263">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="9a980-264">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración del paso anterior abre una nueva pestaña del depurador.</span><span class="sxs-lookup"><span data-stu-id="9a980-264">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="9a980-265">Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.</span><span class="sxs-lookup"><span data-stu-id="9a980-265">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="9a980-266">En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código.</span><span class="sxs-lookup"><span data-stu-id="9a980-266">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="9a980-267">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="9a980-267">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="9a980-268">:::no-loc(Blazor)::: proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="9a980-268">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="9a980-269">Cuando se presiona el método abreviado de teclado de depuración, :::no-loc(Blazor)::: apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="9a980-269">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="9a980-270">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="9a980-270">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="9a980-271">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="9a980-271">Browser source maps</span></span>

<span data-ttu-id="9a980-272">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9a980-272">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="9a980-273">Sin embargo, en la actualidad :::no-loc(Blazor)::: no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="9a980-273">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="9a980-274">En su lugar, :::no-loc(Blazor)::: realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="9a980-274">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9a980-275">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="9a980-275">Troubleshoot</span></span>

<span data-ttu-id="9a980-276">Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:</span><span class="sxs-lookup"><span data-stu-id="9a980-276">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="9a980-277">En la pestaña **Depurador** , abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9a980-277">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="9a980-278">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9a980-278">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="9a980-279">Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza.</span><span class="sxs-lookup"><span data-stu-id="9a980-279">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="9a980-280">Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="9a980-280">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="9a980-281">Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**.</span><span class="sxs-lookup"><span data-stu-id="9a980-281">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="9a980-282">Es el valor predeterminado para Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9a980-282">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="9a980-283">Si la depuración no funciona, confirme que la opción está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="9a980-283">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="9a980-284">Si su entorno usa un proxy HTTP, asegúrese de que `localhost` esté incluido en la configuración de omisión del proxy.</span><span class="sxs-lookup"><span data-stu-id="9a980-284">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="9a980-285">Esto puede hacerse al establecer la variable de entorno `NO_PROXY` en alguna de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="9a980-285">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="9a980-286">Archivo `launchSettings.json` del proyecto.</span><span class="sxs-lookup"><span data-stu-id="9a980-286">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="9a980-287">En el nivel de usuario o de entorno del sistema para que se aplique a todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="9a980-287">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="9a980-288">Si usa una variable de entorno, reinicie Visual Studio para que el cambio surta efecto.</span><span class="sxs-lookup"><span data-stu-id="9a980-288">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="9a980-289">Puntos de interrupción de `OnInitialized{Async}` no ejecutados</span><span class="sxs-lookup"><span data-stu-id="9a980-289">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="9a980-290">El proxy de depuración del marco de trabajo de :::no-loc(Blazor)::: tarda poco tiempo en iniciarse, por lo que es posible que no se ejecuten los puntos de interrupción en el [método de ciclo de vida `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="9a980-290">The :::no-loc(Blazor)::: framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="9a980-291">Se recomienda agregar un retraso al principio del cuerpo del método para dar al proxy de depuración un tiempo para que se inicie antes de que se ejecute el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9a980-291">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="9a980-292">Puede incluir el retraso según una [directiva de compilador `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para asegurarse de que el retraso no está presente en una compilación de versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a980-292">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="9a980-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="9a980-293"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="9a980-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="9a980-294"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="9a980-295">Tiempo de espera de Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="9a980-295">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="9a980-296">Si Visual Studio inicia una excepción relacionada con que el adaptador de depuración no ha podido iniciarse en la que se menciona que se ha alcanzado el tiempo de espera, puede ajustar el tiempo de espera con una configuración del Registro:</span><span class="sxs-lookup"><span data-stu-id="9a980-296">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging ":::no-loc(Blazor):::TimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="9a980-297">El marcador de posición `{TIMEOUT}` del comando anterior se expresa en milisegundos.</span><span class="sxs-lookup"><span data-stu-id="9a980-297">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="9a980-298">Por ejemplo, un minuto se asigna como `60000`.</span><span class="sxs-lookup"><span data-stu-id="9a980-298">For example, one minute is assigned as `60000`.</span></span>
