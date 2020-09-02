---
title: Depuración de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
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
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876781"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="9b9a1-103">Depuración de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b9a1-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="9b9a1-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="9b9a1-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="9b9a1-105">Las aplicaciones Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="9b9a1-106">También puede depurar la aplicación con los siguientes entornos de desarrollo integrado (IDE):</span><span class="sxs-lookup"><span data-stu-id="9b9a1-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="9b9a1-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b9a1-107">Visual Studio</span></span>
* <span data-ttu-id="9b9a1-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9b9a1-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="9b9a1-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b9a1-109">Visual Studio Code</span></span>

<span data-ttu-id="9b9a1-110">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-110">Available scenarios include:</span></span>

* <span data-ttu-id="9b9a1-111">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="9b9a1-112">Ejecute la aplicación con compatibilidad de depuración en IDE.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="9b9a1-113">Examine el código en un solo paso.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-113">Single-step through the code.</span></span>
* <span data-ttu-id="9b9a1-114">Reanude la ejecución de código con un método abreviado de teclado en IDE.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="9b9a1-115">En la ventana *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="9b9a1-116">Vea la pila de llamadas, incluidas las cadenas de llamadas entre JavaScript y .NET.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="9b9a1-117">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-117">For now, you *can't*:</span></span>

* <span data-ttu-id="9b9a1-118">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="9b9a1-119">Alcanzar puntos de interrupción durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9b9a1-120">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b9a1-121">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9b9a1-121">Prerequisites</span></span>

<span data-ttu-id="9b9a1-122">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="9b9a1-123">Google Chrome (versión 70 o posterior) (predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9b9a1-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="9b9a1-124">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="9b9a1-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="9b9a1-125">Visual Studio para Mac requiere la versión 8.8 (compilación 1532) o una posterior:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="9b9a1-126">Instale la versión más reciente de Visual Studio para Mac seleccionando el botón **Descargar Visual Studio para Mac** en [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="9b9a1-127">Seleccione el canal *Vista preliminar* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="9b9a1-128">Para obtener más información, vea [Instalación de la versión más reciente de Visual Studio para Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="9b9a1-129">Apple Safari en macOS no se admite actualmente.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="9b9a1-130">Habilitar la depuración</span><span class="sxs-lookup"><span data-stu-id="9b9a1-130">Enable debugging</span></span>

<span data-ttu-id="9b9a1-131">Para habilitar la depuración de una aplicación Blazor WebAssembly, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="9b9a1-132">Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="9b9a1-133">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="9b9a1-134">Permite que el IDE detecte que la aplicación es una aplicación Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="9b9a1-135">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="9b9a1-136">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b9a1-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b9a1-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b9a1-138">Para depurar una aplicación Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="9b9a1-139">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="9b9a1-140">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9b9a1-141">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9b9a1-142">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9b9a1-143">En la aplicación *Cliente*, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="9b9a1-144">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="9b9a1-145">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="9b9a1-146">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="9b9a1-147">Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="9b9a1-148">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9b9a1-149">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9b9a1-150">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="9b9a1-151">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="9b9a1-152">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="9b9a1-153">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9b9a1-154">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9b9a1-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b9a1-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="9b9a1-156">Depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9b9a1-156">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="9b9a1-157">Abra la aplicación independiente Blazor WebAssembly en VS Code.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-157">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="9b9a1-158">Puede recibir una notificación que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-158">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="9b9a1-159">Se necesita una configuración adicional para depurar aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-159">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="9b9a1-160">Si recibe la notificación:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-160">If you receive the notification:</span></span>

   * <span data-ttu-id="9b9a1-161">Confirme que está instalada la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-161">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="9b9a1-162">Para inspeccionar las extensiones instaladas, abra **Ver** > **Extensiones** en la barra de menús o seleccione el icono **Extensiones** en la barra lateral **Actividad**.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-162">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="9b9a1-163">Confirme que está habilitada la depuración de JavaScript en versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-163">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="9b9a1-164">Abra la configuración en la barra de menús (**Archivo** > **Preferencias** > **Configuración**).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-164">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="9b9a1-165">Busque con las palabras clave `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-165">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="9b9a1-166">En los resultados de la búsqueda, confirme que la casilla **Depurar > JavaScript: Usar vista previa** está activada.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-166">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="9b9a1-167">Si la opción para habilitar la depuración de vista previa no está presente, actualice a la versión más reciente de VS Code o instale la [extensión del depurador de JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code 1.46 o versiones anteriores).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-167">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="9b9a1-168">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-168">Reload the window.</span></span>

1. <span data-ttu-id="9b9a1-169">Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-169">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9b9a1-170">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-170">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="9b9a1-171">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-171">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="9b9a1-172">Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-172">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="9b9a1-173">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-173">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="9b9a1-174">En la aplicación *Cliente*, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-174">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="9b9a1-175">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-175">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="9b9a1-176">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-176">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9b9a1-177">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-177">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="9b9a1-178">Depuración de una aplicación hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9b9a1-178">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="9b9a1-179">Abra la carpeta de la solución de la aplicación Blazor WebAssembly hospedada en VS Code.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-179">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="9b9a1-180">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-180">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="9b9a1-181">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-181">Select **Yes**.</span></span>

   > <span data-ttu-id="9b9a1-182">Faltan los recursos necesarios para compilar y depurar desde "{NOMBRE DE LA APLICACIÓN}".</span><span class="sxs-lookup"><span data-stu-id="9b9a1-182">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="9b9a1-183">Add them?"</span><span class="sxs-lookup"><span data-stu-id="9b9a1-183">Add them?</span></span>

1. <span data-ttu-id="9b9a1-184">En la paleta de comandos de la parte superior de la ventana, seleccione el proyecto *Server* en la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-184">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="9b9a1-185">Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-185">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="9b9a1-186">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="9b9a1-186">Attach to an existing debugging session</span></span>

<span data-ttu-id="9b9a1-187">Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-187">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="9b9a1-188">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-188">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="9b9a1-189">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-189">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="9b9a1-190">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="9b9a1-190">Launch configuration options</span></span>

<span data-ttu-id="9b9a1-191">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm` (`.vscode/launch.json`).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-191">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="9b9a1-192">Opción</span><span class="sxs-lookup"><span data-stu-id="9b9a1-192">Option</span></span>    | <span data-ttu-id="9b9a1-193">Descripción</span><span class="sxs-lookup"><span data-stu-id="9b9a1-193">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="9b9a1-194">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-194">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="9b9a1-195">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-195">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="9b9a1-196">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-196">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="9b9a1-197">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-197">The browser to launch for the debugging session.</span></span> <span data-ttu-id="9b9a1-198">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-198">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="9b9a1-199">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-199">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="9b9a1-200">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-200">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="9b9a1-201">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-201">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="9b9a1-202">Se debe establecer en `true` si se inicia y depura una aplicación hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-202">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="9b9a1-203">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-203">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="9b9a1-204">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-204">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="9b9a1-205">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-205">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="9b9a1-206">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-206">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="9b9a1-207">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-207">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="9b9a1-208">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-208">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="9b9a1-209">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-209">The working directory to launch the app under.</span></span> <span data-ttu-id="9b9a1-210">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-210">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="9b9a1-211">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-211">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="9b9a1-212">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-212">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="9b9a1-213">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="9b9a1-213">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="9b9a1-214">Inicio y depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9b9a1-214">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="9b9a1-215">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="9b9a1-215">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="9b9a1-216">Inicio y depuración de una aplicación Blazor WebAssembly hospedada con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9b9a1-216">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="9b9a1-217">La configuración predeterminada del explorador es Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-217">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="9b9a1-218">Al usar Microsoft Edge para la depuración, establezca `browser` en `edge`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-218">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="9b9a1-219">Para usar Google Chrome, no establezca la opción `browser` ni establezca el valor de la opción en `chrome`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-219">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="9b9a1-220">En el ejemplo anterior, `MyHostedApp.Server.dll` es el ensamblado de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-220">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="9b9a1-221">La carpeta `.vscode` se encuentra en la carpeta de la solución junto a las carpetas `Client`, `Server` y `Shared`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-221">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9b9a1-222">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9b9a1-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9b9a1-223">Para depurar una aplicación Blazor WebAssembly en Visual Studio para Mac, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-223">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="9b9a1-224">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-224">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="9b9a1-225">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-225">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9b9a1-226">No se admite la opción **Iniciar sin depurar** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-226">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="9b9a1-227">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-227">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="9b9a1-228">El explorador seleccionado para la sesión de depuración debe ser Google Chrome o Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-228">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="9b9a1-229">En la aplicación *Cliente*, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-229">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="9b9a1-230">En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-230">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="9b9a1-231">En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-231">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="9b9a1-232">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-232">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="9b9a1-233">Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-233">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="9b9a1-234">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-234">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="9b9a1-235">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-235">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="9b9a1-236">Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-236">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="9b9a1-237">Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-237">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="9b9a1-238">Vuelva a presionar <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="9b9a1-239">Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-239">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="9b9a1-240">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-240">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="9b9a1-241">Para obtener más información, vea [Depuración con Visual Studio para Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-241">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="9b9a1-242">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="9b9a1-242">Debug in the browser</span></span>

<span data-ttu-id="9b9a1-243">*La guía de esta sección se aplica a Google Chrome y Microsoft Edge en Windows.*</span><span class="sxs-lookup"><span data-stu-id="9b9a1-243">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="9b9a1-244">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-244">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="9b9a1-245">Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-245">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="9b9a1-246">En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-246">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="9b9a1-247">El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-247">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="9b9a1-248">Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-248">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="9b9a1-249">Siga las instrucciones del explorador, que abre una nueva ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-249">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="9b9a1-250">Cierre la ventana anterior del explorador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-250">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="9b9a1-251">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración del paso anterior abre una nueva pestaña del depurador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-251">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="9b9a1-252">Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-252">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="9b9a1-253">En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-253">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="9b9a1-254">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-254">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="9b9a1-255">Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-255">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="9b9a1-256">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-256">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="9b9a1-257">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-257">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="9b9a1-258">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="9b9a1-258">Browser source maps</span></span>

<span data-ttu-id="9b9a1-259">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-259">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="9b9a1-260">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-260">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="9b9a1-261">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-261">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9b9a1-262">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="9b9a1-262">Troubleshoot</span></span>

<span data-ttu-id="9b9a1-263">Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-263">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="9b9a1-264">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-264">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="9b9a1-265">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-265">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="9b9a1-266">Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-266">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="9b9a1-267">Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-267">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="9b9a1-268">Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-268">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="9b9a1-269">Es el valor predeterminado para Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-269">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="9b9a1-270">Si la depuración no funciona, confirme que la opción está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-270">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="9b9a1-271">Puntos de interrupción de `OnInitialized{Async}` no ejecutados</span><span class="sxs-lookup"><span data-stu-id="9b9a1-271">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="9b9a1-272">El proxy de depuración del marco de trabajo de Blazor tarda poco tiempo en iniciarse, por lo que es posible que no se ejecuten los puntos de interrupción en el [método de ciclo de vida `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="9b9a1-272">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="9b9a1-273">Se recomienda agregar un retraso al principio del cuerpo del método para dar al proxy de depuración un tiempo para que se inicie antes de que se ejecute el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-273">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="9b9a1-274">Puede incluir el retraso según una [directiva de compilador `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para asegurarse de que el retraso no está presente en una compilación de versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-274">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="9b9a1-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="9b9a1-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="9b9a1-277">Tiempo de espera de Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="9b9a1-277">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="9b9a1-278">Si Visual Studio inicia una excepción relacionada con que el adaptador de depuración no ha podido iniciarse en la que se menciona que se ha alcanzado el tiempo de espera, puede ajustar el tiempo de espera con una configuración del Registro:</span><span class="sxs-lookup"><span data-stu-id="9b9a1-278">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="9b9a1-279">El marcador de posición `{TIMEOUT}` del comando anterior se expresa en milisegundos.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-279">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="9b9a1-280">Por ejemplo, un minuto se asigna como `60000`.</span><span class="sxs-lookup"><span data-stu-id="9b9a1-280">For example, one minute is assigned as `60000`.</span></span>
