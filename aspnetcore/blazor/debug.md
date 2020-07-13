---
title: Depuración de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: c48eb19c5a1759aace112e2afb1637c649173a3d
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059908"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="8c5fc-103">Depuración de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c5fc-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="8c5fc-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="8c5fc-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="8c5fc-105">Las aplicaciones Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="8c5fc-106">También puede depurar la aplicación con Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="8c5fc-107">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-107">Available scenarios include:</span></span>

* <span data-ttu-id="8c5fc-108">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="8c5fc-109">Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="8c5fc-110">Un solo paso (<kbd>F10</kbd>) por el código.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="8c5fc-111">Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="8c5fc-112">En la pantalla *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="8c5fc-113">Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="8c5fc-114">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-114">For now, you *can't*:</span></span>

* <span data-ttu-id="8c5fc-115">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="8c5fc-116">Alcanzar puntos de interrupción durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="8c5fc-117">Seguiremos mejorando la experiencia de depuración en las próximas versiones.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c5fc-118">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="8c5fc-118">Prerequisites</span></span>

<span data-ttu-id="8c5fc-119">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="8c5fc-120">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="8c5fc-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="8c5fc-121">Google Chrome (versión 70 o posterior)</span><span class="sxs-lookup"><span data-stu-id="8c5fc-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="8c5fc-122">Habilitación de la depuración para Visual Studio y Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c5fc-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="8c5fc-123">Para habilitar la depuración de una aplicación Blazor WebAssembly, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="8c5fc-124">Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="8c5fc-125">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="8c5fc-126">Permite que el IDE detecte que la aplicación es una aplicación Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="8c5fc-127">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="8c5fc-128">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="8c5fc-129">Programa para la mejora</span><span class="sxs-lookup"><span data-stu-id="8c5fc-129">Visual Studio</span></span>

<span data-ttu-id="8c5fc-130">Para depurar una aplicación Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="8c5fc-131">Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="8c5fc-132">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="8c5fc-133">Establezca un punto de interrupción en `Pages/Counter.razor` en el método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="8c5fc-134">Vaya a la pestaña **`Counter`** y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="8c5fc-136">Revise el valor del campo `currentCount` en la ventana de variables locales:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="8c5fc-138">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="8c5fc-139">Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="8c5fc-140">Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="8c5fc-141">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="8c5fc-142">Vaya a la pestaña **`Fetch Data`** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="8c5fc-144">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="8c5fc-146">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="8c5fc-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c5fc-147">Visual Studio Code</span></span>

<span data-ttu-id="8c5fc-148">Para obtener información sobre la instalación de Visual Studio Code para el desarrollo de aplicaciones Blazor, vea <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-148">For information on installing Visual Studio Code for Blazor app development, see <xref:blazor/tooling>.</span></span>

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="8c5fc-149">Depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8c5fc-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="8c5fc-150">Abra la aplicación independiente Blazor WebAssembly en VS Code.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="8c5fc-151">Si recibe la notificación siguiente que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-151">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="8c5fc-152">Confirme que tiene instaladas las extensiones correctas.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-152">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="8c5fc-153">Confirme que está habilitada la depuración de JavaScript en versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-153">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="8c5fc-154">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-154">Reload the window.</span></span>

   ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="8c5fc-156">Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-156">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="8c5fc-157">Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-157">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opciones de depuración disponibles](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="8c5fc-159">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-159">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="8c5fc-160">Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-160">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="8c5fc-162">Depuración de una aplicación hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8c5fc-162">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="8c5fc-163">Abra la aplicación hospedada Blazor WebAssembly en VS Code.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-163">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="8c5fc-164">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-164">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="8c5fc-165">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-165">Select **Yes**.</span></span>

   ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="8c5fc-167">En la ventana de selección, seleccione el proyecto *Servidor* dentro de la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-167">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="8c5fc-168">Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-168">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="8c5fc-169">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="8c5fc-169">Attach to an existing debugging session</span></span>

<span data-ttu-id="8c5fc-170">Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-170">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="8c5fc-171">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-171">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="8c5fc-172">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-172">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="8c5fc-173">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="8c5fc-173">Launch configuration options</span></span>

<span data-ttu-id="8c5fc-174">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-174">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="8c5fc-175">Opción</span><span class="sxs-lookup"><span data-stu-id="8c5fc-175">Option</span></span>    | <span data-ttu-id="8c5fc-176">Descripción</span><span class="sxs-lookup"><span data-stu-id="8c5fc-176">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="8c5fc-177">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-177">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="8c5fc-178">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-178">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="8c5fc-179">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-179">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="8c5fc-180">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-180">The browser to launch for the debugging session.</span></span> <span data-ttu-id="8c5fc-181">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-181">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="8c5fc-182">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-182">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="8c5fc-183">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-183">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="8c5fc-184">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-184">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="8c5fc-185">Se debe establecer en `true` si se inicia y depura una aplicación hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-185">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="8c5fc-186">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-186">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="8c5fc-187">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-187">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="8c5fc-188">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-188">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="8c5fc-189">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-189">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="8c5fc-190">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-190">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="8c5fc-191">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-191">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="8c5fc-192">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-192">The working directory to launch the app under.</span></span> <span data-ttu-id="8c5fc-193">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-193">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="8c5fc-194">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-194">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="8c5fc-195">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-195">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="8c5fc-196">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8c5fc-196">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="8c5fc-197">Inicio y depuración de una aplicación independiente Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8c5fc-197">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="8c5fc-198">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="8c5fc-198">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="8c5fc-199">Inicio y depuración de una aplicación hospedada Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8c5fc-199">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="8c5fc-200">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="8c5fc-200">Debug in the browser</span></span>

1. <span data-ttu-id="8c5fc-201">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-201">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="8c5fc-202">Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-202">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="8c5fc-203">En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-203">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="8c5fc-204">El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-204">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="8c5fc-205">Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-205">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="8c5fc-206">Siga las instrucciones del explorador, que abre una nueva ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-206">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="8c5fc-207">Cierre la ventana anterior del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-207">Close the previous browser window.</span></span>

1. <span data-ttu-id="8c5fc-208">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración (<kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) abre una nueva pestaña del depurador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-208">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="8c5fc-209">Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-209">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="8c5fc-210">En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-210">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="8c5fc-211">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-211">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="8c5fc-212"> proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-212"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="8c5fc-213">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-213">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="8c5fc-214">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="8c5fc-214">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="8c5fc-215">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="8c5fc-215">Browser source maps</span></span>

<span data-ttu-id="8c5fc-216">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-216">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="8c5fc-217">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-217">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="8c5fc-218">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-218">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8c5fc-219">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="8c5fc-219">Troubleshoot</span></span>

<span data-ttu-id="8c5fc-220">Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:</span><span class="sxs-lookup"><span data-stu-id="8c5fc-220">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="8c5fc-221">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-221">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="8c5fc-222">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-222">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="8c5fc-223">Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-223">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="8c5fc-224">Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-224">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="8c5fc-225">Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-225">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="8c5fc-226">Es el valor predeterminado para Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-226">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="8c5fc-227">Si la depuración no funciona, confirme que la opción está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="8c5fc-227">If debugging isn't working, confirm that the option is selected.</span></span>
