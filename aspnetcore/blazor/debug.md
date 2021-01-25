---
title: Depuración de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 5bdfcc5660b4c897d3552d4cf25e43dade71541c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252518"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="f6608-103">Depuración de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6608-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="f6608-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f6608-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="f6608-105">Las aplicaciones Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="f6608-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="f6608-106">También puede depurar la aplicación con los siguientes entornos de desarrollo integrado (IDE):</span><span class="sxs-lookup"><span data-stu-id="f6608-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="f6608-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6608-107">Visual Studio</span></span>
* <span data-ttu-id="f6608-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6608-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="f6608-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6608-109">Visual Studio Code</span></span>

<span data-ttu-id="f6608-110">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="f6608-110">Available scenarios include:</span></span>

* <span data-ttu-id="f6608-111">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="f6608-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f6608-112">Ejecute la aplicación con compatibilidad de depuración en IDE.</span><span class="sxs-lookup"><span data-stu-id="f6608-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="f6608-113">Examine el código en un solo paso.</span><span class="sxs-lookup"><span data-stu-id="f6608-113">Single-step through the code.</span></span>
* <span data-ttu-id="f6608-114">Reanude la ejecución de código con un método abreviado de teclado en IDE.</span><span class="sxs-lookup"><span data-stu-id="f6608-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="f6608-115">En la ventana *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="f6608-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="f6608-116">Vea la pila de llamadas, incluidas las cadenas de llamadas entre JavaScript y .NET.</span><span class="sxs-lookup"><span data-stu-id="f6608-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="f6608-117">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="f6608-117">For now, you *can't*:</span></span>

* <span data-ttu-id="f6608-118">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="f6608-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f6608-119">Alcanzar puntos de interrupción durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f6608-120">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="f6608-121">Depure en escenarios no locales; por ejemplo, el [Subsistema de Windows para Linux (WSL)](/windows/wsl/) o [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline).</span><span class="sxs-lookup"><span data-stu-id="f6608-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="f6608-122">Recompile automáticamente la aplicación `*Server*` de back-end de una solución Blazor hospedada durante la depuración, por ejemplo, mediante la ejecución de la aplicación con [`dotnet watch run`](xref:tutorials/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="f6608-122">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6608-123">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="f6608-123">Prerequisites</span></span>

<span data-ttu-id="f6608-124">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="f6608-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f6608-125">Google Chrome (versión 70 o posterior) (predeterminado)</span><span class="sxs-lookup"><span data-stu-id="f6608-125">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="f6608-126">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="f6608-126">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="f6608-127">Asegúrese de que los firewalls o servidores proxy no bloquean la comunicación con el proxy de depuración (proceso `NodeJS`).</span><span class="sxs-lookup"><span data-stu-id="f6608-127">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="f6608-128">Para más información, vea la sección [Configuración de firewall](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="f6608-128">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="f6608-129">Visual Studio para Mac requiere la versión 8.8 (compilación 1532) o una posterior:</span><span class="sxs-lookup"><span data-stu-id="f6608-129">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="f6608-130">Instale la versión más reciente de Visual Studio para Mac seleccionando el botón **Descargar Visual Studio para Mac** en [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="f6608-130">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="f6608-131">Seleccione el canal *Vista preliminar* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f6608-131">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="f6608-132">Para obtener más información, vea [Instalación de la versión más reciente de Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="f6608-132">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="f6608-133">Apple Safari en macOS no se admite actualmente.</span><span class="sxs-lookup"><span data-stu-id="f6608-133">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="f6608-134">Habilitar la depuración</span><span class="sxs-lookup"><span data-stu-id="f6608-134">Enable debugging</span></span>

<span data-ttu-id="f6608-135">Para habilitar la depuración de una aplicación Blazor WebAssembly, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="f6608-135">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f6608-136">Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6608-136">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f6608-137">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="f6608-137">The `inspectUri` property:</span></span>

* <span data-ttu-id="f6608-138">Permite que el IDE detecte que la aplicación es una aplicación Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f6608-138">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f6608-139">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="f6608-139">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="f6608-140">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="f6608-140">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6608-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6608-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6608-142">Para depurar una aplicación Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f6608-142">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f6608-143">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6608-143">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f6608-144">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="f6608-144">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f6608-145">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="f6608-145">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="f6608-146">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f6608-146">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="f6608-147">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="f6608-147">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="f6608-148">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="f6608-148">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="f6608-149">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="f6608-149">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="f6608-150">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="f6608-150">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f6608-151">Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="f6608-151">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="f6608-152">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="f6608-152">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f6608-153">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="f6608-153">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f6608-154">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="f6608-154">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="f6608-155">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="f6608-155">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="f6608-156">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="f6608-156">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="f6608-157">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-157">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f6608-158">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-158">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="f6608-159">Si la aplicación se hospeda en una [ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path) distinta de `/`, actualice las propiedades siguientes en `Properties/launchSettings.json` para reflejar la ruta de acceso base de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f6608-159">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="f6608-160">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="f6608-160">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="f6608-161">`inspectUri` de cada perfil:</span><span class="sxs-lookup"><span data-stu-id="f6608-161">`inspectUri` of each profile:</span></span>

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

<span data-ttu-id="f6608-162">Los marcadores de posición en la configuración anterior:</span><span class="sxs-lookup"><span data-stu-id="f6608-162">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="f6608-163">`{INSECURE PORT}`: puerto no seguro.</span><span class="sxs-lookup"><span data-stu-id="f6608-163">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="f6608-164">De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.</span><span class="sxs-lookup"><span data-stu-id="f6608-164">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="f6608-165">`{APP BASE PATH}`: ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-165">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="f6608-166">`{SECURE PORT}`: puerto seguro.</span><span class="sxs-lookup"><span data-stu-id="f6608-166">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="f6608-167">De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.</span><span class="sxs-lookup"><span data-stu-id="f6608-167">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="f6608-168">`{PROFILE 1, 2, ... N}`: perfiles de configuración de inicio.</span><span class="sxs-lookup"><span data-stu-id="f6608-168">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="f6608-169">Normalmente, una aplicación especifica más de un perfil de forma predeterminada (por ejemplo, un perfil para IIS Express y un perfil de proyecto, que se usa en el servidor de Kestrel).</span><span class="sxs-lookup"><span data-stu-id="f6608-169">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="f6608-170">En los ejemplos siguientes, la aplicación se hospeda en `/OAT` con una ruta de acceso base de la aplicación configurada en `wwwroot/index.html` como `<base href="/OAT/">`:</span><span class="sxs-lookup"><span data-stu-id="f6608-170">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="f6608-171">Para obtener información sobre el uso de una ruta de acceso base de la aplicación personalizada para las aplicaciones de Blazor WebAssembly, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f6608-171">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6608-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6608-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="f6608-173">Depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f6608-173">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="f6608-174">Abra la aplicación independiente Blazor WebAssembly en VS Code.</span><span class="sxs-lookup"><span data-stu-id="f6608-174">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="f6608-175">Puede recibir una notificación que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="f6608-175">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="f6608-176">Se necesita una configuración adicional para depurar aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f6608-176">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="f6608-177">Si recibe la notificación:</span><span class="sxs-lookup"><span data-stu-id="f6608-177">If you receive the notification:</span></span>

   * <span data-ttu-id="f6608-178">Confirme que está instalada la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="f6608-178">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="f6608-179">Para inspeccionar las extensiones instaladas, abra **Ver** > **Extensiones** en la barra de menús o seleccione el icono **Extensiones** en la barra lateral **Actividad**.</span><span class="sxs-lookup"><span data-stu-id="f6608-179">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="f6608-180">Confirme que está habilitada la depuración de JavaScript en versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="f6608-180">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="f6608-181">Abra la configuración en la barra de menús (**Archivo** > **Preferencias** > **Configuración**).</span><span class="sxs-lookup"><span data-stu-id="f6608-181">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="f6608-182">Busque con las palabras clave `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="f6608-182">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="f6608-183">En los resultados de la búsqueda, confirme que la casilla **Depurar > JavaScript: Usar vista previa** está activada.</span><span class="sxs-lookup"><span data-stu-id="f6608-183">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="f6608-184">Si la opción para habilitar la depuración de vista previa no está presente, actualice a la versión más reciente de VS Code o instale la [extensión del depurador de JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code 1.46 o versiones anteriores).</span><span class="sxs-lookup"><span data-stu-id="f6608-184">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="f6608-185">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="f6608-185">Reload the window.</span></span>

1. <span data-ttu-id="f6608-186">Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.</span><span class="sxs-lookup"><span data-stu-id="f6608-186">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f6608-187">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="f6608-187">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="f6608-188">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f6608-188">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="f6608-189">Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-189">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="f6608-190">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-190">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="f6608-191">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="f6608-191">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="f6608-192">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="f6608-192">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="f6608-193">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-193">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f6608-194">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-194">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="f6608-195">Depuración de una aplicación hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f6608-195">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="f6608-196">Abra la carpeta de la solución de la aplicación Blazor WebAssembly hospedada en VS Code.</span><span class="sxs-lookup"><span data-stu-id="f6608-196">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="f6608-197">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="f6608-197">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="f6608-198">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="f6608-198">Select **Yes**.</span></span>

   > <span data-ttu-id="f6608-199">Faltan los recursos necesarios para compilar y depurar desde "{NOMBRE DE LA APLICACIÓN}".</span><span class="sxs-lookup"><span data-stu-id="f6608-199">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="f6608-200">Add them?"</span><span class="sxs-lookup"><span data-stu-id="f6608-200">Add them?</span></span>

1. <span data-ttu-id="f6608-201">En la paleta de comandos de la parte superior de la ventana, seleccione el proyecto *Server* en la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="f6608-201">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="f6608-202">Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="f6608-202">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="f6608-203">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="f6608-203">Attach to an existing debugging session</span></span>

<span data-ttu-id="f6608-204">Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6608-204">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="f6608-205">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="f6608-205">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="f6608-206">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="f6608-206">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="f6608-207">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="f6608-207">Launch configuration options</span></span>

<span data-ttu-id="f6608-208">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm` (`.vscode/launch.json`).</span><span class="sxs-lookup"><span data-stu-id="f6608-208">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="f6608-209">Opción</span><span class="sxs-lookup"><span data-stu-id="f6608-209">Option</span></span>    | <span data-ttu-id="f6608-210">Descripción</span><span class="sxs-lookup"><span data-stu-id="f6608-210">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="f6608-211">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="f6608-211">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="f6608-212">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-212">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="f6608-213">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f6608-213">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="f6608-214">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-214">The browser to launch for the debugging session.</span></span> <span data-ttu-id="f6608-215">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f6608-215">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="f6608-216">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f6608-216">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="f6608-217">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="f6608-217">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="f6608-218">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="f6608-218">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="f6608-219">Se debe establecer en `true` si se inicia y depura una aplicación hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f6608-219">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="f6608-220">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="f6608-220">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="f6608-221">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="f6608-221">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="f6608-222">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-222">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="f6608-223">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="f6608-223">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="f6608-224">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="f6608-224">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="f6608-225">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="f6608-225">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="f6608-226">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-226">The working directory to launch the app under.</span></span> <span data-ttu-id="f6608-227">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="f6608-227">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="f6608-228">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="f6608-228">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="f6608-229">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="f6608-229">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="f6608-230">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f6608-230">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="f6608-231">Inicio y depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f6608-231">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="f6608-232">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="f6608-232">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="f6608-233">Inicio y depuración de una aplicación Blazor WebAssembly hospedada con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="f6608-233">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="f6608-234">La configuración predeterminada del explorador es Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="f6608-234">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="f6608-235">Al usar Microsoft Edge para la depuración, establezca `browser` en `edge`.</span><span class="sxs-lookup"><span data-stu-id="f6608-235">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="f6608-236">Para usar Google Chrome, no establezca la opción `browser` ni establezca el valor de la opción en `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f6608-236">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="f6608-237">En el ejemplo anterior, `MyHostedApp.Server.dll` es el ensamblado de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="f6608-237">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="f6608-238">La carpeta `.vscode` se encuentra en la carpeta de la solución junto a las carpetas `Client`, `Server` y `Shared`.</span><span class="sxs-lookup"><span data-stu-id="f6608-238">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6608-239">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6608-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6608-240">Para depurar una aplicación Blazor WebAssembly en Visual Studio para Mac, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6608-240">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="f6608-241">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6608-241">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f6608-242">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="f6608-242">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f6608-243">No se admite la opción **Iniciar sin depurar** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="f6608-243">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="f6608-244">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f6608-244">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="f6608-245">El explorador seleccionado para la sesión de depuración debe ser Google Chrome o Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="f6608-245">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="f6608-246">En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="f6608-246">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="f6608-247">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="f6608-247">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="f6608-248">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="f6608-248">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="f6608-249">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="f6608-249">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="f6608-250">Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="f6608-250">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="f6608-251">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="f6608-251">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f6608-252">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="f6608-252">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f6608-253">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="f6608-253">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="f6608-254">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="f6608-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="f6608-255">Vuelva a presionar <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="f6608-255">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="f6608-256">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6608-256">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f6608-257">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-257">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="f6608-258">Para obtener más información, vea [Depuración con Visual Studio para Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="f6608-258">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="f6608-259">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="f6608-259">Debug in the browser</span></span>

<span data-ttu-id="f6608-260">*La guía de esta sección se aplica a Google Chrome y Microsoft Edge en Windows.*</span><span class="sxs-lookup"><span data-stu-id="f6608-260">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="f6608-261">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="f6608-261">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f6608-262">Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="f6608-262">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="f6608-263">En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="f6608-263">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="f6608-264">El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f6608-264">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="f6608-265">Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto.</span><span class="sxs-lookup"><span data-stu-id="f6608-265">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="f6608-266">Siga las instrucciones del explorador, que abre una nueva ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="f6608-266">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="f6608-267">Cierre la ventana anterior del explorador.</span><span class="sxs-lookup"><span data-stu-id="f6608-267">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="f6608-268">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración del paso anterior abre una nueva pestaña del depurador.</span><span class="sxs-lookup"><span data-stu-id="f6608-268">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="f6608-269">Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.</span><span class="sxs-lookup"><span data-stu-id="f6608-269">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="f6608-270">En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código.</span><span class="sxs-lookup"><span data-stu-id="f6608-270">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="f6608-271">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="f6608-271">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="f6608-272">Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="f6608-272">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f6608-273">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="f6608-273">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f6608-274">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="f6608-274">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f6608-275">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="f6608-275">Browser source maps</span></span>

<span data-ttu-id="f6608-276">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="f6608-276">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f6608-277">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="f6608-277">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f6608-278">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="f6608-278">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="f6608-279">Configuración de firewall</span><span class="sxs-lookup"><span data-stu-id="f6608-279">Firewall configuration</span></span>

<span data-ttu-id="f6608-280">Si un firewall bloquea la comunicación con el proxy de depuración, cree una regla de excepción de firewall que permita la comunicación entre el explorador y el proceso `NodeJS`.</span><span class="sxs-lookup"><span data-stu-id="f6608-280">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="f6608-281">La modificación de una configuración de firewall debe realizarse con cuidado para evitar la creación de vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="f6608-281">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="f6608-282">Aplique con cuidado las instrucciones de seguridad, siga las recomendaciones de seguridad y respete las advertencias realizadas por el fabricante del firewall.</span><span class="sxs-lookup"><span data-stu-id="f6608-282">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="f6608-283">Permitir la comunicación abierta con el proceso `NodeJS`:</span><span class="sxs-lookup"><span data-stu-id="f6608-283">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="f6608-284">Abre el servidor de Node en cualquier conexión, en función de la configuración y las funcionalidades del firewall.</span><span class="sxs-lookup"><span data-stu-id="f6608-284">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="f6608-285">Podría ser arriesgado en función de la red.</span><span class="sxs-lookup"><span data-stu-id="f6608-285">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="f6608-286">**Solo se recomienda en los equipos de los desarrolladores**.</span><span class="sxs-lookup"><span data-stu-id="f6608-286">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="f6608-287">Si es posible, permita solo la comunicación abierta con el proceso `NodeJS` **en redes privadas o de confianza**.</span><span class="sxs-lookup"><span data-stu-id="f6608-287">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="f6608-288">Para obtener instrucciones de configuración del [Firewall de Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security), vea [Crear una regla de programa o servicio de entrada](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="f6608-288">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="f6608-289">Para más información, vea [Firewall de Windows Defender con seguridad avanzada](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) y los artículos relacionados en la serie de documentos sobre el Firewall de Windows.</span><span class="sxs-lookup"><span data-stu-id="f6608-289">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f6608-290">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="f6608-290">Troubleshoot</span></span>

<span data-ttu-id="f6608-291">Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:</span><span class="sxs-lookup"><span data-stu-id="f6608-291">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="f6608-292">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="f6608-292">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f6608-293">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="f6608-293">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="f6608-294">Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza.</span><span class="sxs-lookup"><span data-stu-id="f6608-294">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="f6608-295">Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="f6608-295">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="f6608-296">Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**.</span><span class="sxs-lookup"><span data-stu-id="f6608-296">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="f6608-297">Es el valor predeterminado para Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f6608-297">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="f6608-298">Si la depuración no funciona, confirme que la opción está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="f6608-298">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="f6608-299">Si su entorno usa un proxy HTTP, asegúrese de que `localhost` esté incluido en la configuración de omisión del proxy.</span><span class="sxs-lookup"><span data-stu-id="f6608-299">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="f6608-300">Esto puede hacerse al establecer la variable de entorno `NO_PROXY` en alguna de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="f6608-300">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="f6608-301">Archivo `launchSettings.json` del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f6608-301">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="f6608-302">En el nivel de usuario o de entorno del sistema para que se aplique a todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="f6608-302">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="f6608-303">Si usa una variable de entorno, reinicie Visual Studio para que el cambio surta efecto.</span><span class="sxs-lookup"><span data-stu-id="f6608-303">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="f6608-304">Asegúrese de que los firewalls o servidores proxy no bloquean la comunicación con el proxy de depuración (proceso `NodeJS`).</span><span class="sxs-lookup"><span data-stu-id="f6608-304">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="f6608-305">Para más información, vea la sección [Configuración de firewall](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="f6608-305">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="f6608-306">Puntos de interrupción de `OnInitialized{Async}` no ejecutados</span><span class="sxs-lookup"><span data-stu-id="f6608-306">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="f6608-307">El proxy de depuración del marco de trabajo de Blazor tarda poco tiempo en iniciarse, por lo que es posible que no se ejecuten los puntos de interrupción en el [método de ciclo de vida `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="f6608-307">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="f6608-308">Se recomienda agregar un retraso al principio del cuerpo del método para dar al proxy de depuración un tiempo para que se inicie antes de que se ejecute el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="f6608-308">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="f6608-309">Puede incluir el retraso según una [directiva de compilador `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para asegurarse de que el retraso no está presente en una compilación de versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f6608-309">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="f6608-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="f6608-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="f6608-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="f6608-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="f6608-312">Tiempo de espera de Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="f6608-312">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="f6608-313">Si Visual Studio inicia una excepción relacionada con que el adaptador de depuración no ha podido iniciarse en la que se menciona que se ha alcanzado el tiempo de espera, puede ajustar el tiempo de espera con una configuración del Registro:</span><span class="sxs-lookup"><span data-stu-id="f6608-313">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="f6608-314">El marcador de posición `{TIMEOUT}` del comando anterior se expresa en milisegundos.</span><span class="sxs-lookup"><span data-stu-id="f6608-314">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="f6608-315">Por ejemplo, un minuto se asigna como `60000`.</span><span class="sxs-lookup"><span data-stu-id="f6608-315">For example, one minute is assigned as `60000`.</span></span>
