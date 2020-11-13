---
title: Prueba de las API web con HttpRepl
author: scottaddie
description: Obtenga información sobre cómo usar la herramienta global HttpRepl de .NET Core para examinar y probar una API web de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/11/2020
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
uid: web-api/http-repl
ms.openlocfilehash: df2d4e63a18471b4c5f4f1c9434921303bb1da8a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550626"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="e27f4-103">Prueba de las API web con HttpRepl</span><span class="sxs-lookup"><span data-stu-id="e27f4-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="e27f4-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e27f4-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e27f4-105">El bucle HTTP read-eval-print (REPL):</span><span class="sxs-lookup"><span data-stu-id="e27f4-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="e27f4-106">Es una herramienta de línea de comandos ligera y multiplataforma con la misma compatibilidad que .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e27f4-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="e27f4-107">Se usa para realizar solicitudes HTTP con el fin de probar las API web de ASP.NET Core (así como otras API) y ver los resultados.</span><span class="sxs-lookup"><span data-stu-id="e27f4-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="e27f4-108">Capaz de probar las API web hospedadas en cualquier entorno, incluidos localhost y Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e27f4-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="e27f4-109">Se admiten los siguientes [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods):</span><span class="sxs-lookup"><span data-stu-id="e27f4-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="e27f4-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="e27f4-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="e27f4-111">GET</span><span class="sxs-lookup"><span data-stu-id="e27f4-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="e27f4-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="e27f4-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="e27f4-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="e27f4-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="e27f4-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="e27f4-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="e27f4-115">POST</span><span class="sxs-lookup"><span data-stu-id="e27f4-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="e27f4-116">PUT</span><span class="sxs-lookup"><span data-stu-id="e27f4-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="e27f4-117">Para continuar, [vea o descargue la API web de muestra de ASP.NET Core ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([cómo descargar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e27f4-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e27f4-118">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e27f4-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="e27f4-119">Instalación</span><span class="sxs-lookup"><span data-stu-id="e27f4-119">Installation</span></span>

<span data-ttu-id="e27f4-120">Ejecute el siguiente comando para instalar HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="e27f4-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="e27f4-121">Se instala una [herramienta global de .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) desde el paquete NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="e27f4-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="e27f4-122">Uso</span><span class="sxs-lookup"><span data-stu-id="e27f4-122">Usage</span></span>

<span data-ttu-id="e27f4-123">Tras la correcta instalación de la herramienta, ejecute el siguiente comando para iniciar HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="e27f4-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="e27f4-124">Para ver los comandos de HttpRepl disponibles, ejecute uno de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="e27f4-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="e27f4-125">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="e27f4-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="e27f4-126">HttpRepl ofrece la finalización del comando.</span><span class="sxs-lookup"><span data-stu-id="e27f4-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="e27f4-127">Al presionar la tecla <kbd>Tabulador</kbd>, se procesa una iteración en la lista de comandos que completa los caracteres o el punto de conexión de la API que ha escrito.</span><span class="sxs-lookup"><span data-stu-id="e27f4-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="e27f4-128">En las secciones siguientes se describen los comandos disponibles de la CLI.</span><span class="sxs-lookup"><span data-stu-id="e27f4-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="e27f4-129">Conexión a la API web</span><span class="sxs-lookup"><span data-stu-id="e27f4-129">Connect to the web API</span></span>

<span data-ttu-id="e27f4-130">Conéctese a la API web ejecutando el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="e27f4-131">`<ROOT URI>` es el URI base de la API web.</span><span class="sxs-lookup"><span data-stu-id="e27f4-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="e27f4-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="e27f4-133">También puede ejecutar el comando siguiente en cualquier momento mientras se ejecuta HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="e27f4-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="e27f4-134">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="e27f4-135">Elección manual de la descripción de OpenAPI para la API web</span><span class="sxs-lookup"><span data-stu-id="e27f4-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="e27f4-136">El comando de conexión anterior intentará buscar la descripción de OpenAPI automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e27f4-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="e27f4-137">Si por alguna razón no puede hacerlo, puede especificar el URI de la descripción de OpenAPI para la API web mediante la opción `--openapi`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="e27f4-138">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="e27f4-139">Habilitación de la salida detallada para obtener más información acerca de la búsqueda, el análisis y la validación de la descripción de OpenAPI</span><span class="sxs-lookup"><span data-stu-id="e27f4-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="e27f4-140">La especificación de la opción `--verbose` con el comando `connect` producirá más detalles cuando la herramienta busque la descripción de OpenAPI y la analice y valide.</span><span class="sxs-lookup"><span data-stu-id="e27f4-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="e27f4-141">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="e27f4-142">Navegación de la API web</span><span class="sxs-lookup"><span data-stu-id="e27f4-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="e27f4-143">Visualización de los puntos de conexión disponibles</span><span class="sxs-lookup"><span data-stu-id="e27f4-143">View available endpoints</span></span>

<span data-ttu-id="e27f4-144">Para enumerar los diferentes puntos de conexión (controladores) en la ruta de acceso actual de la dirección de la API web, ejecute el comando `ls` o `dir`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="e27f4-145">Se muestra el siguiente formato de salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="e27f4-146">La salida anterior indica que hay dos controladores disponibles: `Fruits` y `People`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="e27f4-147">Ambos controladores admiten operaciones HTTP GET y POST sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="e27f4-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="e27f4-148">Al navegar a un controlador específico, se revela más información.</span><span class="sxs-lookup"><span data-stu-id="e27f4-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="e27f4-149">Por ejemplo, la salida del comando siguiente muestra que el controlador `Fruits` también admite las operaciones HTTP GET, PUT y DELETE.</span><span class="sxs-lookup"><span data-stu-id="e27f4-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="e27f4-150">Cada una de estas operaciones esperan un parámetro `id` en la ruta:</span><span class="sxs-lookup"><span data-stu-id="e27f4-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="e27f4-151">También puede ejecutar el comando `ui` para abrir la página de la interfaz de usuario de Swagger de la API web en un explorador.</span><span class="sxs-lookup"><span data-stu-id="e27f4-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="e27f4-152">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="e27f4-153">Navegación a un punto de conexión</span><span class="sxs-lookup"><span data-stu-id="e27f4-153">Navigate to an endpoint</span></span>

<span data-ttu-id="e27f4-154">Para navegar a un punto de conexión distinto en la API web, ejecute el comando `cd`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="e27f4-155">La ruta de acceso que sigue al comando `cd` no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="e27f4-156">Se muestra el siguiente formato de salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="e27f4-157">Personalización de HttpRepl</span><span class="sxs-lookup"><span data-stu-id="e27f4-157">Customize the HttpRepl</span></span>

<span data-ttu-id="e27f4-158">Los [colores](#set-color-preferences) predeterminados de HttpRepl se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="e27f4-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="e27f4-159">Además, se puede definir un [editor de texto predeterminado](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="e27f4-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="e27f4-160">Las preferencias de HttpRepl se conservan tanto en la sesión actual como en futuras sesiones.</span><span class="sxs-lookup"><span data-stu-id="e27f4-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="e27f4-161">Una vez modificadas, se almacenan en el archivo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="e27f4-162">Linux</span><span class="sxs-lookup"><span data-stu-id="e27f4-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="e27f4-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e27f4-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="e27f4-164">macOS</span><span class="sxs-lookup"><span data-stu-id="e27f4-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="e27f4-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e27f4-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="e27f4-166">Windows</span><span class="sxs-lookup"><span data-stu-id="e27f4-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="e27f4-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e27f4-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="e27f4-168">El archivo *.httpreplprefs* se carga al inicio; los cambios no se supervisan durante el tiempo en ejecución.</span><span class="sxs-lookup"><span data-stu-id="e27f4-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="e27f4-169">Las modificaciones manuales que se hagan en el archivo solo se aplicarán después de reiniciar la herramienta.</span><span class="sxs-lookup"><span data-stu-id="e27f4-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="e27f4-170">Visualización de la configuración</span><span class="sxs-lookup"><span data-stu-id="e27f4-170">View the settings</span></span>

<span data-ttu-id="e27f4-171">Para ver la configuración disponible, ejecute el comando `pref get`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="e27f4-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="e27f4-173">El comando anterior muestra los pares clave-valor disponibles:</span><span class="sxs-lookup"><span data-stu-id="e27f4-173">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="e27f4-174">Establecimiento de las preferencias de color</span><span class="sxs-lookup"><span data-stu-id="e27f4-174">Set color preferences</span></span>

<span data-ttu-id="e27f4-175">Actualmente solo se permite colorear la respuesta para JSON.</span><span class="sxs-lookup"><span data-stu-id="e27f4-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="e27f4-176">Para personalizar el color predeterminado de la herramienta HttpRepl, busque la clave correspondiente al color que se va a cambiar.</span><span class="sxs-lookup"><span data-stu-id="e27f4-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="e27f4-177">Para obtener instrucciones sobre cómo buscar las claves, consulte la sección [Visualización de la configuración](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="e27f4-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="e27f4-178">Por ejemplo, cambie el valor de clave `colors.json` de `Green` a `White`, tal como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="e27f4-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="e27f4-179">Solo se pueden usar los [colores permitidos](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs).</span><span class="sxs-lookup"><span data-stu-id="e27f4-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="e27f4-180">Las solicitudes HTTP posteriores muestran la salida con el nuevo color.</span><span class="sxs-lookup"><span data-stu-id="e27f4-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="e27f4-181">Si no se establecen claves de color específicas, se consideran las más genéricas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="e27f4-182">Para mostrar este comportamiento de reserva, veamos el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="e27f4-183">Si `colors.json.name` no tiene ningún valor, se usa `colors.json.string`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="e27f4-184">Si `colors.json.string` no tiene ningún valor, se usa `colors.json.literal`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="e27f4-185">Si `colors.json.literal` no tiene ningún valor, se usa `colors.json`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="e27f4-186">Si `colors.json` no tiene ningún valor, se usa el color de texto predeterminado del shell del comando (`AllowedColors.None`).</span><span class="sxs-lookup"><span data-stu-id="e27f4-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="e27f4-187">Establecimiento del tamaño de sangría</span><span class="sxs-lookup"><span data-stu-id="e27f4-187">Set indentation size</span></span>

<span data-ttu-id="e27f4-188">Actualmente, la personalización del tamaño de sangría de respuesta solo se admite para JSON.</span><span class="sxs-lookup"><span data-stu-id="e27f4-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="e27f4-189">El tamaño predeterminado es de dos espacios.</span><span class="sxs-lookup"><span data-stu-id="e27f4-189">The default size is two spaces.</span></span> <span data-ttu-id="e27f4-190">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-190">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="e27f4-191">Para cambiar el tamaño predeterminado, establezca la clave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="e27f4-192">Por ejemplo, para usar siempre cuatro espacios:</span><span class="sxs-lookup"><span data-stu-id="e27f4-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="e27f4-193">Las respuestas posteriores respetan el valor de cuatro espacios:</span><span class="sxs-lookup"><span data-stu-id="e27f4-193">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="e27f4-194">Establecimiento del editor de texto predeterminado</span><span class="sxs-lookup"><span data-stu-id="e27f4-194">Set the default text editor</span></span>

<span data-ttu-id="e27f4-195">De manera predeterminada, HttpRepl no tiene ningún editor de texto configurado para su uso.</span><span class="sxs-lookup"><span data-stu-id="e27f4-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="e27f4-196">Para probar los métodos de la API web que requieren un cuerpo de la solicitud HTTP, se debe establecer un editor de texto predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="e27f4-197">La herramienta HttpRepl inicia el editor de texto configurado con el único fin de redactar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e27f4-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="e27f4-198">Ejecute el comando siguiente para establecer el editor de texto preferido como predeterminado:</span><span class="sxs-lookup"><span data-stu-id="e27f4-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="e27f4-199">En el comando anterior, `<EXECUTABLE>` es la ruta de acceso completa al archivo ejecutable del editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e27f4-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="e27f4-200">Por ejemplo, ejecute el comando siguiente para establecer Visual Studio Code como editor de texto predeterminado:</span><span class="sxs-lookup"><span data-stu-id="e27f4-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="e27f4-201">Linux</span><span class="sxs-lookup"><span data-stu-id="e27f4-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="e27f4-202">macOS</span><span class="sxs-lookup"><span data-stu-id="e27f4-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="e27f4-203">Windows</span><span class="sxs-lookup"><span data-stu-id="e27f4-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="e27f4-204">Para iniciar el editor de texto predeterminado con argumentos específicos de la CLI, establezca la clave `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="e27f4-205">Por ejemplo, supongamos que Visual Studio Code es el editor de texto predeterminado y que siempre quiere que HttpRepl abra Visual Studio Code en una nueva sesión con las extensiones deshabilitadas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="e27f4-206">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e27f4-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="e27f4-207">Si su editor predeterminado es Visual Studio Code, normalmente querrá pasar el argumento `-w` o `--wait` para hacer que Visual Studio Code le espere para cerrar el archivo antes de volver.</span><span class="sxs-lookup"><span data-stu-id="e27f4-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="e27f4-208">Establecimiento de las rutas de acceso de búsqueda de la descripción de OpenAPI</span><span class="sxs-lookup"><span data-stu-id="e27f4-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="e27f4-209">De forma predeterminada, HttpRepl tiene un conjunto de rutas de acceso relativas que usa para buscar la descripción de OpenAPI al ejecutar el comando `connect` sin la opción `--openapi`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="e27f4-210">Estas rutas de acceso relativas se combinan con las rutas de acceso raíz y base especificadas en el comando `connect`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="e27f4-211">Las rutas de acceso relativas predeterminadas son:</span><span class="sxs-lookup"><span data-stu-id="e27f4-211">The default relative paths are:</span></span>

- <span data-ttu-id="e27f4-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-212">*swagger.json*</span></span>
- <span data-ttu-id="e27f4-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="e27f4-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-214">*/swagger.json*</span></span>
- <span data-ttu-id="e27f4-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="e27f4-216">*openapi.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-216">*openapi.json*</span></span>
- <span data-ttu-id="e27f4-217">*/openapi.json*</span><span class="sxs-lookup"><span data-stu-id="e27f4-217">*/openapi.json*</span></span>

<span data-ttu-id="e27f4-218">Para usar un conjunto diferente de rutas de acceso de búsqueda en el entorno, establezca la preferencia `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="e27f4-219">El valor debe ser una lista delimitada por canalizaciones de rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="e27f4-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="e27f4-221">En lugar de reemplazar la lista predeterminada por completo, la lista también se puede modificar agregando o quitando rutas de acceso.</span><span class="sxs-lookup"><span data-stu-id="e27f4-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="e27f4-222">Para agregar una o varias rutas de acceso de búsqueda a la lista predeterminada, establezca la preferencia `swagger.addToSearchPaths`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="e27f4-223">El valor debe ser una lista delimitada por canalizaciones de rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="e27f4-224">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="e27f4-225">Para quitar una o varias rutas de acceso de búsqueda de la lista predeterminada, establezca la preferencia `swagger.addToSearchPaths`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="e27f4-226">El valor debe ser una lista delimitada por canalizaciones de rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="e27f4-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="e27f4-227">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="e27f4-228">Prueba de solicitudes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="e27f4-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-229">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-230">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-231">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-232">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-232">Options</span></span>

<span data-ttu-id="e27f4-233">Las siguientes opciones están disponibles para el comando `get`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="e27f4-234">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e27f4-234">Example</span></span>

<span data-ttu-id="e27f4-235">Para emitir una solicitud HTTP GET realice lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="e27f4-236">Ejecute el comando `get` en un punto de conexión que lo admita:</span><span class="sxs-lookup"><span data-stu-id="e27f4-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="e27f4-237">El comando anterior muestra el siguiente formato de salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-237">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="e27f4-238">Recupere un registro específico pasando un parámetro al comando `get`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="e27f4-239">El comando anterior muestra el siguiente formato de salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-239">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="e27f4-240">Prueba de solicitudes HTTP POST</span><span class="sxs-lookup"><span data-stu-id="e27f4-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-241">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-242">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-243">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-244">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="e27f4-245">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e27f4-245">Example</span></span>

<span data-ttu-id="e27f4-246">Para emitir una solicitud HTTP POST, realice lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="e27f4-247">Ejecute el comando `post` en un punto de conexión que lo admita:</span><span class="sxs-lookup"><span data-stu-id="e27f4-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="e27f4-248">En el comando anterior, el encabezado de solicitud HTTP `Content-Type` se establece para indicar un tipo de medios de cuerpo de la solicitud de JSON.</span><span class="sxs-lookup"><span data-stu-id="e27f4-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="e27f4-249">El editor de texto predeterminado abre un archivo *.tmp* con una plantilla JSON que representa el cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="e27f4-250">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="e27f4-251">Para establecer el editor de texto predeterminado, consulte la sección [Establecimiento del editor de texto predeterminado](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="e27f4-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="e27f4-252">Modifique la plantilla JSON para cumplir los requisitos de validación de modelos:</span><span class="sxs-lookup"><span data-stu-id="e27f4-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="e27f4-253">Guarde el archivo *.tmp* y cierre el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e27f4-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="e27f4-254">En el shell del comando aparecerá la salida siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-254">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="e27f4-255">Prueba de solicitudes HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="e27f4-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-256">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-257">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-258">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-259">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="e27f4-260">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e27f4-260">Example</span></span>

<span data-ttu-id="e27f4-261">Para emitir una solicitud HTTP PUT, realice lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="e27f4-262">*Opcional* : Ejecute el comando `get` para ver los datos antes de modificarlos:</span><span class="sxs-lookup"><span data-stu-id="e27f4-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="e27f4-263">Ejecute el comando `put` en un punto de conexión que lo admita:</span><span class="sxs-lookup"><span data-stu-id="e27f4-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="e27f4-264">En el comando anterior, el encabezado de solicitud HTTP `Content-Type` se establece para indicar un tipo de medios de cuerpo de la solicitud de JSON.</span><span class="sxs-lookup"><span data-stu-id="e27f4-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="e27f4-265">El editor de texto predeterminado abre un archivo *.tmp* con una plantilla JSON que representa el cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="e27f4-266">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="e27f4-267">Para establecer el editor de texto predeterminado, consulte la sección [Establecimiento del editor de texto predeterminado](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="e27f4-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="e27f4-268">Modifique la plantilla JSON para cumplir los requisitos de validación de modelos:</span><span class="sxs-lookup"><span data-stu-id="e27f4-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="e27f4-269">Guarde el archivo *.tmp* y cierre el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e27f4-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="e27f4-270">En el shell del comando aparecerá la salida siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="e27f4-271">*Opcional* : Emita un comando `get` para ver las modificaciones.</span><span class="sxs-lookup"><span data-stu-id="e27f4-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="e27f4-272">Por ejemplo, si ha escrito "Cherry" en el editor de texto, un elemento `get` devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="e27f4-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="e27f4-273">Prueba de solicitudes HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="e27f4-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-274">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-275">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-276">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-277">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="e27f4-278">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e27f4-278">Example</span></span>

<span data-ttu-id="e27f4-279">Para emitir una solicitud HTTP DELETE, realice lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="e27f4-280">*Opcional* : Ejecute el comando `get` para ver los datos antes de modificarlos:</span><span class="sxs-lookup"><span data-stu-id="e27f4-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="e27f4-281">Ejecute el comando `delete` en un punto de conexión que lo admita:</span><span class="sxs-lookup"><span data-stu-id="e27f4-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="e27f4-282">El comando anterior muestra el siguiente formato de salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="e27f4-283">*Opcional* : Emita un comando `get` para ver las modificaciones.</span><span class="sxs-lookup"><span data-stu-id="e27f4-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="e27f4-284">En este ejemplo, un elemento `get` devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="e27f4-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="e27f4-285">Prueba de solicitudes HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="e27f4-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-286">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-287">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-288">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-289">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="e27f4-290">Prueba de solicitudes HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="e27f4-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-291">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-292">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-293">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-294">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="e27f4-295">Prueba de solicitudes HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="e27f4-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e27f4-296">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="e27f4-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e27f4-297">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e27f4-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e27f4-298">Se trata del parámetro de ruta, si existe, que espera el método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="e27f4-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e27f4-299">Opciones</span><span class="sxs-lookup"><span data-stu-id="e27f4-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="e27f4-300">Establecimiento de encabezados de solicitudes HTTP</span><span class="sxs-lookup"><span data-stu-id="e27f4-300">Set HTTP request headers</span></span>

<span data-ttu-id="e27f4-301">Para establecer un encabezado de solicitud HTTP, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e27f4-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="e27f4-302">Establézcalo insertado con la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="e27f4-303">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="e27f4-304">Con el método anterior, cada encabezado de solicitud HTTP distinto requiere su propia opción `-h`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="e27f4-305">Establézcalo antes de enviar la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="e27f4-306">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="e27f4-307">Al establecer el encabezado antes de enviar una solicitud, este permanece establecido mientras dure la sesión de shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="e27f4-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="e27f4-308">Para borrar el encabezado, proporcione un valor vacío.</span><span class="sxs-lookup"><span data-stu-id="e27f4-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="e27f4-309">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="e27f4-310">Prueba de los puntos de conexión seguros</span><span class="sxs-lookup"><span data-stu-id="e27f4-310">Test secured endpoints</span></span>

<span data-ttu-id="e27f4-311">HttpRepl admite las pruebas de puntos de conexión protegidos de las siguientes formas:</span><span class="sxs-lookup"><span data-stu-id="e27f4-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="e27f4-312">A través de las credenciales predeterminadas del usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="e27f4-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="e27f4-313">A través del uso de encabezados de solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="e27f4-314">Credenciales predeterminadas</span><span class="sxs-lookup"><span data-stu-id="e27f4-314">Default credentials</span></span>

<span data-ttu-id="e27f4-315">Considere una API web que esté probando y que se hospede en IIS y esté protegida con la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="e27f4-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="e27f4-316">Quiere que las credenciales del usuario que ejecutan la herramienta fluyan a través de los puntos de conexión HTTP en proceso de prueba.</span><span class="sxs-lookup"><span data-stu-id="e27f4-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="e27f4-317">Para pasar las credenciales predeterminadas del usuario que ha iniciado sesión:</span><span class="sxs-lookup"><span data-stu-id="e27f4-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="e27f4-318">Establezca la preferencia `httpClient.useDefaultCredentials` en `true`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="e27f4-319">Salga de la herramienta y reiníciela antes de enviar otra solicitud a la API web.</span><span class="sxs-lookup"><span data-stu-id="e27f4-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="e27f4-320">Credenciales del proxy predeterminadas</span><span class="sxs-lookup"><span data-stu-id="e27f4-320">Default proxy credentials</span></span>

<span data-ttu-id="e27f4-321">Considere un escenario en el que la API web que está probando está detrás de un proxy protegido con la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="e27f4-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="e27f4-322">Quiere que las credenciales del usuario que ejecutan la herramienta fluyan al proxy.</span><span class="sxs-lookup"><span data-stu-id="e27f4-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="e27f4-323">Para pasar las credenciales predeterminadas del usuario que ha iniciado sesión:</span><span class="sxs-lookup"><span data-stu-id="e27f4-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="e27f4-324">Establezca la preferencia `httpClient.proxy.useDefaultCredentials` en `true`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="e27f4-325">Salga de la herramienta y reiníciela antes de enviar otra solicitud a la API web.</span><span class="sxs-lookup"><span data-stu-id="e27f4-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="e27f4-326">Encabezados de solicitud HTTP</span><span class="sxs-lookup"><span data-stu-id="e27f4-326">HTTP request headers</span></span>

<span data-ttu-id="e27f4-327">Entre los ejemplos de esquemas de autenticación y autorización admitidos se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="e27f4-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="e27f4-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="e27f4-328">basic authentication</span></span>
* <span data-ttu-id="e27f4-329">Tokens de portador de JWT</span><span class="sxs-lookup"><span data-stu-id="e27f4-329">JWT bearer tokens</span></span>
* <span data-ttu-id="e27f4-330">autenticación implícita</span><span class="sxs-lookup"><span data-stu-id="e27f4-330">digest authentication</span></span>

<span data-ttu-id="e27f4-331">Por ejemplo, puede enviar un token de portador a un punto de conexión con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="e27f4-332">Para acceder un punto de conexión hospedado por Azure o para usar la [API REST de Azure](/rest/api/azure/), necesitará un token de portador.</span><span class="sxs-lookup"><span data-stu-id="e27f4-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="e27f4-333">Use los pasos siguientes para obtener un token de portador para la suscripción de Azure a través de la [CLI de Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="e27f4-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="e27f4-334">HttpRepl establece el token de portador en un encabezado de solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e27f4-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="e27f4-335">Se recupera una lista de Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="e27f4-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="e27f4-336">Inicie de sesión en Azure:</span><span class="sxs-lookup"><span data-stu-id="e27f4-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="e27f4-337">Obtenga el identificador de suscripción con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="e27f4-338">Copie el identificador de suscripción y ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="e27f4-339">Obtenga el token de portador con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="e27f4-340">Conéctese a la API REST de Azure con HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="e27f4-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="e27f4-341">Establezca el encabezado de solicitud HTTP `Authorization`:</span><span class="sxs-lookup"><span data-stu-id="e27f4-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="e27f4-342">Vaya a la suscripción:</span><span class="sxs-lookup"><span data-stu-id="e27f4-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="e27f4-343">Obtenga una lista de las instancias de Azure App Service Web Apps de su suscripción:</span><span class="sxs-lookup"><span data-stu-id="e27f4-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="e27f4-344">Se muestra la respuesta siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="e27f4-345">Alternancia de la pantalla de solicitud HTTP</span><span class="sxs-lookup"><span data-stu-id="e27f4-345">Toggle HTTP request display</span></span>

<span data-ttu-id="e27f4-346">De manera predeterminada, se suprime la pantalla de solicitud HTTP que se está enviando.</span><span class="sxs-lookup"><span data-stu-id="e27f4-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="e27f4-347">Es posible cambiar la configuración correspondiente mientras dure la sesión de shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="e27f4-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="e27f4-348">Habilitación de la pantalla de solicitudes</span><span class="sxs-lookup"><span data-stu-id="e27f4-348">Enable request display</span></span>

<span data-ttu-id="e27f4-349">Vea la solicitud HTTP que se envía mediante la ejecución del comando `echo on`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="e27f4-350">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="e27f4-351">Las solicitudes HTTP posteriores de la sesión actual muestran los encabezados de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e27f4-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="e27f4-352">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-352">For example:</span></span>

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="e27f4-353">Deshabilitación de la pantalla de solicitudes</span><span class="sxs-lookup"><span data-stu-id="e27f4-353">Disable request display</span></span>

<span data-ttu-id="e27f4-354">Suprima la pantalla de solicitud HTTP que se envía mediante la ejecución del comando `echo off`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="e27f4-355">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="e27f4-356">Ejecutar un script</span><span class="sxs-lookup"><span data-stu-id="e27f4-356">Run a script</span></span>

<span data-ttu-id="e27f4-357">Si ejecuta con frecuencia el mismo conjunto de comandos de HttpRepl, considere la posibilidad de almacenarlos en un archivo de texto.</span><span class="sxs-lookup"><span data-stu-id="e27f4-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="e27f4-358">Los comandos del archivo toman el mismo formulario que los ejecutados manualmente en la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="e27f4-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="e27f4-359">Los comandos se pueden ejecutar en un modo por lotes mediante el comando `run`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="e27f4-360">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-360">For example:</span></span>

1. <span data-ttu-id="e27f4-361">Cree un archivo de texto que contenga un conjunto de comandos delimitados por línea nueva.</span><span class="sxs-lookup"><span data-stu-id="e27f4-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="e27f4-362">Para ilustrarlo, considere la posibilidad de usar un archivo *people-script.txt* que contenga los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e27f4-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="e27f4-363">Ejecute el comando `run`, pasando la ruta de acceso del archivo de texto.</span><span class="sxs-lookup"><span data-stu-id="e27f4-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="e27f4-364">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e27f4-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="e27f4-365">Se mostrará lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="e27f4-366">Borrado de la salida</span><span class="sxs-lookup"><span data-stu-id="e27f4-366">Clear the output</span></span>

<span data-ttu-id="e27f4-367">Para quitar todas las salidas escritas en el shell de comandos mediante la herramienta HttpRepl, ejecute el comando `clear` o `cls`.</span><span class="sxs-lookup"><span data-stu-id="e27f4-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="e27f4-368">Para ilustrarlo, imagine que el shell de comandos contiene la salida siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="e27f4-369">Ejecute el comando siguiente para borrar la salida:</span><span class="sxs-lookup"><span data-stu-id="e27f4-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="e27f4-370">Después de ejecutar el comando anterior, el shell de comandos solo contiene la salida siguiente:</span><span class="sxs-lookup"><span data-stu-id="e27f4-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="e27f4-371">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e27f4-371">Additional resources</span></span>

* [<span data-ttu-id="e27f4-372">Solicitudes de API REST</span><span class="sxs-lookup"><span data-stu-id="e27f4-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="e27f4-373">Repositorio GitHub de HttpRepl</span><span class="sxs-lookup"><span data-stu-id="e27f4-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
