---
title: Depuración de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/17/2020
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
ms.openlocfilehash: 5aeb333dc36ebc4c3a324b397793343e0335b1e1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628370"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="a1cd4-103">Depuración de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1cd4-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="a1cd4-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="a1cd4-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="a1cd4-105">Las aplicaciones Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="a1cd4-106">También puede depurar la aplicación con Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="a1cd4-107">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-107">Available scenarios include:</span></span>

* <span data-ttu-id="a1cd4-108">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a1cd4-109">Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="a1cd4-110">Un solo paso (<kbd>F10</kbd>) por el código.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="a1cd4-111">Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="a1cd4-112">En la pantalla *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="a1cd4-113">Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a1cd4-114">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-114">For now, you *can't*:</span></span>

* <span data-ttu-id="a1cd4-115">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="a1cd4-116">Alcanzar puntos de interrupción durante el inicio de la aplicación antes de que se ejecute el proxy de depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-116">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="a1cd4-117">Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-117">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="a1cd4-118">Seguiremos mejorando la experiencia de depuración en las próximas versiones.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-118">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1cd4-119">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="a1cd4-119">Prerequisites</span></span>

<span data-ttu-id="a1cd4-120">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-120">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a1cd4-121">Google Chrome (versión 70 o posterior) (predeterminado)</span><span class="sxs-lookup"><span data-stu-id="a1cd4-121">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="a1cd4-122">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="a1cd4-122">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="a1cd4-123">Habilitación de la depuración para Visual Studio y Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1cd4-123">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="a1cd4-124">Para habilitar la depuración de una aplicación Blazor WebAssembly, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-124">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="a1cd4-125">Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-125">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="a1cd4-126">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-126">The `inspectUri` property:</span></span>

* <span data-ttu-id="a1cd4-127">Permite que el IDE detecte que la aplicación es una aplicación Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-127">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="a1cd4-128">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-128">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="a1cd4-129">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-129">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="a1cd4-130">Programa para la mejora</span><span class="sxs-lookup"><span data-stu-id="a1cd4-130">Visual Studio</span></span>

<span data-ttu-id="a1cd4-131">Para depurar una aplicación Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-131">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="a1cd4-132">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-132">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="a1cd4-133">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-133">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a1cd4-134">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-134">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="a1cd4-135">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-135">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="a1cd4-136">Establezca un punto de interrupción en `Pages/Counter.razor` en el método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-136">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="a1cd4-137">Vaya a la pestaña **`Counter`** y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-137">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="a1cd4-139">Revise el valor del campo `currentCount` en la ventana de variables locales:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-139">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="a1cd4-141">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-141">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="a1cd4-142">Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-142">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="a1cd4-143">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-143">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="a1cd4-144">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-144">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="a1cd4-145">Vaya a la pestaña **`Fetch Data`** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-145">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="a1cd4-147">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-147">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="a1cd4-149">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-149">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="a1cd4-150">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1cd4-150">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="a1cd4-151">Depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a1cd4-151">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="a1cd4-152">Abra la aplicación independiente Blazor WebAssembly en VS Code.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-152">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="a1cd4-153">Puede recibir la notificación siguiente que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-153">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="a1cd4-155">Si recibe la notificación:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-155">If you receive the notification:</span></span>

   * <span data-ttu-id="a1cd4-156">Confirme que está instalada la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-156">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="a1cd4-157">Para inspeccionar las extensiones instaladas, abra **Ver** > **Extensiones** en la barra de menús o seleccione el icono **Extensiones** en la barra lateral **Actividad**.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-157">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="a1cd4-158">Confirme que está habilitada la depuración de JavaScript en versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-158">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="a1cd4-159">Abra la configuración en la barra de menús (**Archivo** > **Preferencias** > **Configuración**).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-159">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="a1cd4-160">Busque con las palabras clave `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-160">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="a1cd4-161">En los resultados de la búsqueda, confirme que la casilla **Depurar > JavaScript: Usar vista previa** está activada.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-161">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="a1cd4-162">Si la opción para habilitar la depuración de vista previa no está presente, actualice a la versión más reciente de VS Code o instale la [extensión del depurador de JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code 1.46 o versiones anteriores).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-162">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="a1cd4-163">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-163">Reload the window.</span></span>

1. <span data-ttu-id="a1cd4-164">Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a1cd4-165">No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-165">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="a1cd4-166">Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-166">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="a1cd4-167">Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-167">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opciones de depuración disponibles](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="a1cd4-169">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-169">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="a1cd4-170">Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="a1cd4-172">Depuración de una aplicación hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a1cd4-172">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="a1cd4-173">Abra la carpeta de la solución de la aplicación Blazor WebAssembly hospedada en VS Code.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-173">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="a1cd4-174">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-174">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="a1cd4-175">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-175">Select **Yes**.</span></span>

   ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="a1cd4-177">En la paleta de comandos de la parte superior de la ventana, seleccione el proyecto *Server* en la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-177">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="a1cd4-178">Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-178">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="a1cd4-179">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="a1cd4-179">Attach to an existing debugging session</span></span>

<span data-ttu-id="a1cd4-180">Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-180">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="a1cd4-181">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-181">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="a1cd4-182">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-182">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="a1cd4-183">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="a1cd4-183">Launch configuration options</span></span>

<span data-ttu-id="a1cd4-184">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm` (`.vscode/launch.json`).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-184">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="a1cd4-185">Opción</span><span class="sxs-lookup"><span data-stu-id="a1cd4-185">Option</span></span>    | <span data-ttu-id="a1cd4-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="a1cd4-186">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="a1cd4-187">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-187">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="a1cd4-188">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-188">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="a1cd4-189">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-189">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="a1cd4-190">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-190">The browser to launch for the debugging session.</span></span> <span data-ttu-id="a1cd4-191">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-191">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="a1cd4-192">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-192">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="a1cd4-193">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-193">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="a1cd4-194">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-194">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="a1cd4-195">Se debe establecer en `true` si se inicia y depura una aplicación hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-195">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="a1cd4-196">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-196">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="a1cd4-197">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-197">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="a1cd4-198">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-198">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="a1cd4-199">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-199">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="a1cd4-200">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-200">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="a1cd4-201">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-201">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="a1cd4-202">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-202">The working directory to launch the app under.</span></span> <span data-ttu-id="a1cd4-203">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-203">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="a1cd4-204">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-204">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="a1cd4-205">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-205">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="a1cd4-206">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="a1cd4-206">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="a1cd4-207">Inicio y depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a1cd4-207">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="a1cd4-208">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="a1cd4-208">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="a1cd4-209">Inicio y depuración de una aplicación Blazor WebAssembly hospedada con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a1cd4-209">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="a1cd4-210">La configuración predeterminada del explorador es Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-210">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="a1cd4-211">Al usar Microsoft Edge para la depuración, establezca `browser` en `edge`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-211">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="a1cd4-212">Para usar Google Chrome, no establezca la opción `browser` ni establezca el valor de la opción en `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-212">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="a1cd4-213">En el ejemplo anterior, `MyHostedApp.Server.dll` es el ensamblado de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-213">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="a1cd4-214">La carpeta `.vscode` se encuentra en la carpeta de la solución junto a las carpetas `Client`, `Server` y `Shared`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-214">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="a1cd4-215">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="a1cd4-215">Debug in the browser</span></span>

1. <span data-ttu-id="a1cd4-216">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-216">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="a1cd4-217">Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-217">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="a1cd4-218">En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-218">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="a1cd4-219">El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-219">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="a1cd4-220">Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-220">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="a1cd4-221">Siga las instrucciones del explorador, que abre una nueva ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-221">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="a1cd4-222">Cierre la ventana anterior del explorador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-222">Close the previous browser window.</span></span>

1. <span data-ttu-id="a1cd4-223">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración (<kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) abre una nueva pestaña del depurador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-223">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="a1cd4-224">Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-224">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="a1cd4-225">En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-225">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="a1cd4-226">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-226">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="a1cd4-227">Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-227">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a1cd4-228">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-228">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a1cd4-229">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-229">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a1cd4-230">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="a1cd4-230">Browser source maps</span></span>

<span data-ttu-id="a1cd4-231">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-231">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a1cd4-232">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-232">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a1cd4-233">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-233">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a1cd4-234">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="a1cd4-234">Troubleshoot</span></span>

<span data-ttu-id="a1cd4-235">Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-235">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="a1cd4-236">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-236">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a1cd4-237">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-237">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="a1cd4-238">Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-238">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="a1cd4-239">Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-239">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="a1cd4-240">Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-240">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="a1cd4-241">Es el valor predeterminado para Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-241">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="a1cd4-242">Si la depuración no funciona, confirme que la opción está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-242">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="a1cd4-243">Puntos de interrupción de `OnInitialized{Async}` no ejecutados</span><span class="sxs-lookup"><span data-stu-id="a1cd4-243">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="a1cd4-244">El proxy de depuración del marco de trabajo de Blazor tarda poco tiempo en iniciarse, por lo que es posible que no se ejecuten los puntos de interrupción en el [método de ciclo de vida `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="a1cd4-244">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="a1cd4-245">Se recomienda agregar un retraso al principio del cuerpo del método para dar al proxy de depuración un tiempo para que se inicie antes de que se ejecute el punto de interrupción.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-245">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="a1cd4-246">Puede incluir el retraso según una [directiva de compilador `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para asegurarse de que el retraso no está presente en una compilación de versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-246">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="a1cd4-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="a1cd4-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-248"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-timeout"></a><span data-ttu-id="a1cd4-249">Tiempo de espera de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1cd4-249">Visual Studio timeout</span></span>

<span data-ttu-id="a1cd4-250">Si Visual Studio inicia una excepción relacionada con que el adaptador de depuración no ha podido iniciarse en la que se menciona que se ha alcanzado el tiempo de espera, puede ajustar el tiempo de espera con una configuración del Registro:</span><span class="sxs-lookup"><span data-stu-id="a1cd4-250">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="a1cd4-251">El marcador de posición `{TIMEOUT}` del comando anterior se expresa en milisegundos.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-251">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="a1cd4-252">Por ejemplo, un minuto se asigna como `60000`.</span><span class="sxs-lookup"><span data-stu-id="a1cd4-252">For example, one minute is assigned as `60000`.</span></span>
