---
title: Uso de la plantilla de proyecto de Angular con ASP.NET Core
author: SteveSandersonMS
description: Aprenda cómo comenzar a trabajar con la plantilla de proyecto de aplicación de página única (SPA) de ASP.NET Core para Angular y la CLI de Angular.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/angular
ms.openlocfilehash: ce29d891b0fba2581d352637c2ae50ac792b785e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013169"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="801ba-103">Uso de la plantilla de proyecto de Angular con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="801ba-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="801ba-104">La plantilla de proyecto de Angular actualizada proporciona un práctico punto de partida para las aplicaciones ASP.NET Core que usan Angular y la CLI de Angular para implementar una completa interfaz de usuario (UI) del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="801ba-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="801ba-105">La plantilla es equivalente a crear un proyecto de ASP.NET Core que funciona como back-end de API y un proyecto de la CLI de Angular que funciona como interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="801ba-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="801ba-106">La plantilla ofrece la ventaja de hospedar ambos tipos de proyecto en un único proyecto de aplicación.</span><span class="sxs-lookup"><span data-stu-id="801ba-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="801ba-107">Por lo tanto, el proyecto de aplicación se puede compilar y publicar como una sola unidad.</span><span class="sxs-lookup"><span data-stu-id="801ba-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="801ba-108">Creación de una nueva aplicación</span><span class="sxs-lookup"><span data-stu-id="801ba-108">Create a new app</span></span>

<span data-ttu-id="801ba-109">Si tiene ASP.NET Core 2.1 instalado, no es necesario instalar la plantilla de proyecto de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="801ba-110">En un símbolo del sistema, cree un nuevo proyecto con el comando `dotnet new angular` en un directorio vacío.</span><span class="sxs-lookup"><span data-stu-id="801ba-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="801ba-111">Por ejemplo, los siguientes comandos crean la aplicación en un directorio *my-new-app* y cambian a ese directorio:</span><span class="sxs-lookup"><span data-stu-id="801ba-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="801ba-112">Ejecute la aplicación desde Visual Studio o la CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="801ba-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="801ba-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="801ba-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="801ba-114">Abra el archivo *.csproj* generado y, desde ahí, ejecute la aplicación de la manera habitual.</span><span class="sxs-lookup"><span data-stu-id="801ba-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="801ba-115">El proceso de compilación restaura las dependencias npm en la primera ejecución, lo que puede tardar varios minutos.</span><span class="sxs-lookup"><span data-stu-id="801ba-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="801ba-116">Las compilaciones posteriores son mucho más rápidas.</span><span class="sxs-lookup"><span data-stu-id="801ba-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="801ba-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="801ba-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="801ba-118">Asegúrese de tener una variable de entorno denominada `ASPNETCORE_Environment` con un valor de `Development`.</span><span class="sxs-lookup"><span data-stu-id="801ba-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="801ba-119">En Windows (en los avisos que no son de PowerShell), ejecute `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="801ba-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="801ba-120">En Linux o macOS, ejecute `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="801ba-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="801ba-121">Ejecute [dotnet build](/dotnet/core/tools/dotnet-build) para comprobar que la aplicación se compila correctamente.</span><span class="sxs-lookup"><span data-stu-id="801ba-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="801ba-122">En la primera ejecución, el proceso de compilación restaura las dependencias de npm, lo que puede tardar varios minutos.</span><span class="sxs-lookup"><span data-stu-id="801ba-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="801ba-123">Las compilaciones posteriores son mucho más rápidas.</span><span class="sxs-lookup"><span data-stu-id="801ba-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="801ba-124">Ejecute [dotnet run](/dotnet/core/tools/dotnet-run) para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="801ba-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="801ba-125">Se registra un mensaje similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="801ba-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="801ba-126">Vaya a esta dirección URL en un explorador.</span><span class="sxs-lookup"><span data-stu-id="801ba-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="801ba-127">La aplicación inicia en segundo plano una instancia del servidor de la CLI de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="801ba-128">Se registra un mensaje similar al siguiente: *El servidor de desarrollo de NG Live escucha en localhost:&lt;otherport&gt;, abra el explorador en http://localhost:&lt;otherport&gt;/* .</span><span class="sxs-lookup"><span data-stu-id="801ba-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="801ba-129">Omita este mensaje, no **es** la dirección URL de la aplicación combinada de ASP.NET Core y la CLI de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="801ba-130">La plantilla de proyecto crea una aplicación ASP.NET Core y una aplicación de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="801ba-131">El uso previsto de la aplicación ASP.NET Core es el acceso a los datos, la autorización y otros problemas relativos al servidor.</span><span class="sxs-lookup"><span data-stu-id="801ba-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="801ba-132">Por otro lado, la aplicación de Angular, que reside en el subdirectorio *ClientApp* está destinada a todos los problemas relacionados con la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="801ba-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="801ba-133">Adición de páginas, imágenes, estilos, módulos, etc.</span><span class="sxs-lookup"><span data-stu-id="801ba-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="801ba-134">El directorio *ClientApp* contiene una aplicación estándar de la CLI de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="801ba-135">Consulte la [documentación de Angular](https://angular.io) para más información.</span><span class="sxs-lookup"><span data-stu-id="801ba-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="801ba-136">Existen pequeñas diferencias entre la aplicación de Angular creada con esta plantilla y la creada con la propia CLI de Angular (mediante `ng new`); sin embargo, las funcionalidades de la aplicación permanecen sin cambios.</span><span class="sxs-lookup"><span data-stu-id="801ba-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="801ba-137">La aplicación creada con la plantilla contiene un diseño basado en [arranque](https://getbootstrap.com/) y un ejemplo de enrutamiento básico.</span><span class="sxs-lookup"><span data-stu-id="801ba-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="801ba-138">Ejecución de comandos ng</span><span class="sxs-lookup"><span data-stu-id="801ba-138">Run ng commands</span></span>

<span data-ttu-id="801ba-139">En un símbolo del sistema, cambie al subdirectorio *ClientApp*:</span><span class="sxs-lookup"><span data-stu-id="801ba-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="801ba-140">Si tiene instalada la herramienta `ng` globalmente, puede ejecutar cualquiera de sus comandos.</span><span class="sxs-lookup"><span data-stu-id="801ba-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="801ba-141">Por ejemplo, puede ejecutar `ng lint`, `ng test`, o cualquiera de los otros [comandos de la CLI de Angular](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="801ba-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="801ba-142">Si bien, no es necesario ejecutar `ng serve`, dado que la aplicación ASP.NET Core se ocupa de atender las partes del lado cliente y servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="801ba-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="801ba-143">Internamente, usa `ng serve` en el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="801ba-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="801ba-144">Si no tiene instalada la herramienta `ng`, ejecute en su lugar `npm run ng`.</span><span class="sxs-lookup"><span data-stu-id="801ba-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="801ba-145">Por ejemplo, puede ejecutar `npm run ng lint` o `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="801ba-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="801ba-146">Instalar paquetes de npm</span><span class="sxs-lookup"><span data-stu-id="801ba-146">Install npm packages</span></span>

<span data-ttu-id="801ba-147">Para instalar paquetes de npm de otro fabricante, use un símbolo del sistema en el subdirectorio *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="801ba-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="801ba-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="801ba-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="801ba-149">Publicación e implementación</span><span class="sxs-lookup"><span data-stu-id="801ba-149">Publish and deploy</span></span>

<span data-ttu-id="801ba-150">En el desarrollo, la aplicación se ejecuta en modo optimizado para comodidad del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="801ba-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="801ba-151">Por ejemplo, las agrupaciones de JavaScript incluyen asignaciones de origen (de modo que, durante la depuración, puede ver el código original de TypeScript).</span><span class="sxs-lookup"><span data-stu-id="801ba-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="801ba-152">La aplicación inspecciona los cambios en los archivos de TypeScript, HTML y CSS en el disco y, automáticamente, realiza una nueva compilación y recarga cuando observa que esos archivos han cambiado.</span><span class="sxs-lookup"><span data-stu-id="801ba-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="801ba-153">En producción, use una versión de la aplicación que esté optimizada para el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="801ba-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="801ba-154">Esto se configura para que tenga lugar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="801ba-154">This is configured to happen automatically.</span></span> <span data-ttu-id="801ba-155">Al publicar, la configuración de compilación emite una compilación Ahead Of Time (AoT) reducida del código del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="801ba-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="801ba-156">A diferencia de la compilación de desarrollo, la compilación de producción no requiere la instalación de Node.js en el servidor, a menos que haya habilitado la representación previa del lado servidor (SSR).</span><span class="sxs-lookup"><span data-stu-id="801ba-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="801ba-157">Puede usar [métodos de implementación y hospedaje de ASP.NET Core](xref:host-and-deploy/index) estándar.</span><span class="sxs-lookup"><span data-stu-id="801ba-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="801ba-158">Ejecución de "ng serve" de manera independiente</span><span class="sxs-lookup"><span data-stu-id="801ba-158">Run "ng serve" independently</span></span>

<span data-ttu-id="801ba-159">El proyecto está configurado para iniciar su propia instancia del servidor de CLI de Angular en segundo plano cuando la aplicación ASP.NET Core se inicia en modo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="801ba-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="801ba-160">Esto resulta útil porque no tiene que ejecutar manualmente un servidor independiente.</span><span class="sxs-lookup"><span data-stu-id="801ba-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="801ba-161">Sin embargo, esta configuración predeterminada tiene un inconveniente.</span><span class="sxs-lookup"><span data-stu-id="801ba-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="801ba-162">Cada vez que modifica el código de C# y la aplicación ASP.NET Core debe reiniciarse, el servidor de CLI de Angular se reinicia.</span><span class="sxs-lookup"><span data-stu-id="801ba-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="801ba-163">Se necesitan unos 10 segundos para iniciar la copia de seguridad.</span><span class="sxs-lookup"><span data-stu-id="801ba-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="801ba-164">Sin realiza frecuentes modificaciones en el código de C# y no quiere esperar a que se reinicie la CLI de Angular, ejecute el servidor de la CLI de Angular externamente, con independencia del proceso de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="801ba-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="801ba-165">Para ello:</span><span class="sxs-lookup"><span data-stu-id="801ba-165">To do so:</span></span>

1. <span data-ttu-id="801ba-166">En un símbolo del sistema, cambie al subdirectorio *ClientApp* e inicie el servidor de desarrollo de la CLI Angular:</span><span class="sxs-lookup"><span data-stu-id="801ba-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="801ba-167">Use `npm start` para iniciar el servidor de desarrollo de la CLI de Angular, no `ng serve`, de modo que la configuración de *package.json* se respete.</span><span class="sxs-lookup"><span data-stu-id="801ba-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="801ba-168">Para pasar parámetros adicionales al servidor de la CLI de Angular, agréguelos a la línea de `scripts` correspondiente de su archivo *package.json*.</span><span class="sxs-lookup"><span data-stu-id="801ba-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="801ba-169">Modifique la aplicación ASP.NET Core para usar la instancia externa de la CLI de Angular en lugar de iniciar una de las suyas.</span><span class="sxs-lookup"><span data-stu-id="801ba-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="801ba-170">En la clase *Startup*, reemplace la invocación de `spa.UseAngularCliServer` por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="801ba-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="801ba-171">Cuando inicie la aplicación ASP.NET Core, no se iniciará un servidor de la CLI de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="801ba-172">En su lugar, se usa la instancia que inició manualmente.</span><span class="sxs-lookup"><span data-stu-id="801ba-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="801ba-173">Esto le permite iniciar y reiniciar con mayor rapidez.</span><span class="sxs-lookup"><span data-stu-id="801ba-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="801ba-174">Ya no tiene que esperar a que la CLI de Angular vuelva a compilar la aplicación cliente una y otra vez.</span><span class="sxs-lookup"><span data-stu-id="801ba-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="801ba-175">Paso de datos del código de .NET al código de TypeScript</span><span class="sxs-lookup"><span data-stu-id="801ba-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="801ba-176">Durante SSR, quizás quiera puede pasar datos por solicitud de la aplicación ASP.NET Core a la aplicación de Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="801ba-177">Por ejemplo, podría pasar información de cookies o algo que se haya leído de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="801ba-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="801ba-178">Para ello, edite la clase *Startup*.</span><span class="sxs-lookup"><span data-stu-id="801ba-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="801ba-179">En la devolución de llamada de `UseSpaPrerendering`, establezca un valor para `options.SupplyData`, como el siguiente:</span><span class="sxs-lookup"><span data-stu-id="801ba-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="801ba-180">La devolución de llamada `SupplyData` permite pasar datos arbitrarios y por solicitud que se pueden serializar con JSON (por ejemplo, cadenas, valores booleanos o números).</span><span class="sxs-lookup"><span data-stu-id="801ba-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="801ba-181">El código *main.server.ts* la recibe como `params.data`.</span><span class="sxs-lookup"><span data-stu-id="801ba-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="801ba-182">Por ejemplo, el ejemplo de código anterior pasa un valor booleano como `params.data.isHttpsRequest` a la devolución de llamada `createServerRenderer`.</span><span class="sxs-lookup"><span data-stu-id="801ba-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="801ba-183">Se puede pasar a otras partes de la aplicación en cualquier modo admitido por Angular.</span><span class="sxs-lookup"><span data-stu-id="801ba-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="801ba-184">Por ejemplo, vea cómo *main.server.ts* pasa el valor `BASE_URL` a cualquier componente cuyo constructor se declara para recibirlo.</span><span class="sxs-lookup"><span data-stu-id="801ba-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="801ba-185">Desventajas de SSR</span><span class="sxs-lookup"><span data-stu-id="801ba-185">Drawbacks of SSR</span></span>

<span data-ttu-id="801ba-186">No todas las aplicaciones se benefician de SSR.</span><span class="sxs-lookup"><span data-stu-id="801ba-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="801ba-187">La principal ventaja es el rendimiento percibido.</span><span class="sxs-lookup"><span data-stu-id="801ba-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="801ba-188">Los visitantes que llegan a la aplicación a través de una conexión de red lenta o en dispositivos móviles lentos verán la interfaz de usuario inicial rápidamente, incluso si tarda un rato en capturar o analizar las agrupaciones de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="801ba-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="801ba-189">Sin embargo, muchas SPA se utilizan principalmente a través de redes de empresa internas rápidas en equipos rápidos donde la aplicación aparece casi al instante.</span><span class="sxs-lookup"><span data-stu-id="801ba-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="801ba-190">Al mismo tiempo, la habilitación de SSR conlleva importantes desventajas:</span><span class="sxs-lookup"><span data-stu-id="801ba-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="801ba-191">Agrega complejidad al proceso de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="801ba-191">It adds complexity to your development process.</span></span> <span data-ttu-id="801ba-192">El código se debe ejecutar en dos entornos diferentes: cliente y servidor (en un entorno de Node.js se invoca desde ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="801ba-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="801ba-193">Hay algunos aspectos a tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="801ba-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="801ba-194">SSR requiere la instalación de Node.js en los servidores de producción.</span><span class="sxs-lookup"><span data-stu-id="801ba-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="801ba-195">En algunos escenarios de implementación esto se produce automáticamente, como Azure App Services, pero no en otros, como Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="801ba-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="801ba-196">Al habilitar la marca de compilación `BuildServerSideRenderer`, el directorio *node_modules* se publica.</span><span class="sxs-lookup"><span data-stu-id="801ba-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="801ba-197">Esta carpeta contiene más de 20 000 archivos, lo que aumenta el tiempo de implementación.</span><span class="sxs-lookup"><span data-stu-id="801ba-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="801ba-198">Para ejecutar el código en un entorno de Node.js, no puede confiar en la existencia de API de JavaScript específicas del explorador como `window` o `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="801ba-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="801ba-199">Si el código (o alguna biblioteca de terceros a la que hace referencia) intenta usar estas API, obtendrá un error durante SSR.</span><span class="sxs-lookup"><span data-stu-id="801ba-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="801ba-200">Por ejemplo, no use jQuery porque hace referencia a API específicas del explorador en muchos lugares.</span><span class="sxs-lookup"><span data-stu-id="801ba-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="801ba-201">Para evitar errores, no use en la medida de lo posible SSR ni API o bibliotecas específicas del explorador.</span><span class="sxs-lookup"><span data-stu-id="801ba-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="801ba-202">Puede encapsular las llamadas a estas API en comprobaciones para asegurarse de que no se invoquen durante SSR.</span><span class="sxs-lookup"><span data-stu-id="801ba-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="801ba-203">Por ejemplo, use una comprobación como la siguiente en código de JavaScript o TypeScript:</span><span class="sxs-lookup"><span data-stu-id="801ba-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="801ba-204">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="801ba-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
