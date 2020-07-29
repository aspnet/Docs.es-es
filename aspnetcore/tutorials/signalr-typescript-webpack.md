---
title: Uso de ASP.NET Core [SignalR con TypeScript and Webpack
author: ssougnez
description: En este tutorial, configurará Webpack para agrupar y compilar una aplicación web ASP.NET Core [SignalR cuyo cliente está escrito en TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 49d185ce941d5f8e841224e7de3b72b8350a1c47
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407907"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="03efd-103">Uso de ASP.NET Core [SignalR con TypeScript and Webpack</span><span class="sxs-lookup"><span data-stu-id="03efd-103">Use ASP.NET Core [SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="03efd-104">Por [Sébastien Sougnez](https://twitter.com/ssougnez) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="03efd-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="03efd-105">[Webpack](https://webpack.js.org/) permite a los desarrolladores agrupar y compilar los recursos del lado cliente de una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="03efd-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="03efd-106">En este tutorial se describe el uso de Webpack en una aplicación web ASP.NET Core [SignalR cuyo cliente está escrito en [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="03efd-106">This tutorial demonstrates using Webpack in an ASP.NET Core [SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="03efd-107">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="03efd-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="03efd-108">Agregar scaffold a una aplicación ASP.NET Core [SignalR de inicio</span><span class="sxs-lookup"><span data-stu-id="03efd-108">Scaffold a starter ASP.NET Core [SignalR app</span></span>
> * <span data-ttu-id="03efd-109">Configurar el cliente de TypeScript para [SignalR</span><span class="sxs-lookup"><span data-stu-id="03efd-109">Configure the [SignalR TypeScript client</span></span>
> * <span data-ttu-id="03efd-110">Configuración de una canalización de compilación mediante Webpack</span><span class="sxs-lookup"><span data-stu-id="03efd-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="03efd-111">Configurar el servidor [SignalR</span><span class="sxs-lookup"><span data-stu-id="03efd-111">Configure the [SignalR server</span></span>
> * <span data-ttu-id="03efd-112">Habilitar la comunicación entre cliente y servidor</span><span class="sxs-lookup"><span data-stu-id="03efd-112">Enable communication between client and server</span></span>

<span data-ttu-id="03efd-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03efd-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="03efd-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="03efd-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="03efd-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="03efd-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="03efd-117">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="03efd-118">[Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="03efd-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="03efd-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="03efd-121">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="03efd-122">C# para Visual Studio Code versión 1.17.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="03efd-123">[Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="03efd-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="03efd-124">Creación de la aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03efd-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03efd-126">Configure Visual Studio para buscar npm en la variable de entorno *PATH*.</span><span class="sxs-lookup"><span data-stu-id="03efd-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="03efd-127">De forma predeterminada, Visual Studio usa la versión de npm que se encuentra en su directorio de instalación.</span><span class="sxs-lookup"><span data-stu-id="03efd-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="03efd-128">Siga estas instrucciones en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="03efd-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="03efd-129">Inicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03efd-129">Launch Visual Studio.</span></span> <span data-ttu-id="03efd-130">En la ventana de inicio, seleccione **Continuar sin código**.</span><span class="sxs-lookup"><span data-stu-id="03efd-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="03efd-131">Vaya a **Herramientas** > **Opciones** > **Proyectos y soluciones** > **Administración de paquetes web** > **Herramientas web externas**.</span><span class="sxs-lookup"><span data-stu-id="03efd-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="03efd-132">Seleccione la entrada *$(PATH)* en la lista.</span><span class="sxs-lookup"><span data-stu-id="03efd-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="03efd-133">Haga clic en la flecha arriba para mover la entrada a la segunda posición de la lista y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Configuración de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="03efd-135">Se ha completado la configuración de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03efd-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="03efd-136">Use la opción de menú **Archivo** > **Nuevo** > **Proyecto** y seleccione la plantilla **Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="03efd-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="03efd-137">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="03efd-137">Select **Next**.</span></span>
1. <span data-ttu-id="03efd-138">Asigne el nombre *SignalRWebPack* al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="03efd-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="03efd-139">Seleccione *.NET Core* en la lista desplegable de plataformas de destino y *ASP.NET Core 3.1* en la lista desplegable del selector de plataformas.</span><span class="sxs-lookup"><span data-stu-id="03efd-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="03efd-140">Seleccione la plantilla **Vacía** y **Crear**.</span><span class="sxs-lookup"><span data-stu-id="03efd-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="03efd-141">Agregue el paquete `Microsoft.TypeScript.MSBuild` al proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="03efd-142">En el **Explorador de soluciones** (panel derecho), haga clic con el botón derecho en el nodo del proyecto y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="03efd-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="03efd-143">En la pestaña **Examinar**, busque `Microsoft.TypeScript.MSBuild` y luego haga clic en **Instalar** a la derecha para instalar el paquete.</span><span class="sxs-lookup"><span data-stu-id="03efd-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="03efd-144">Visual Studio agrega el paquete NuGet en el nodo **Dependencias** del **Explorador de soluciones**, que habilita la compilación de TypeScript en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03efd-146">Ejecute el comando siguiente en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="03efd-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="03efd-147">El comando `dotnet new` crea una aplicación web ASP.NET Core vacía en el directorio *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="03efd-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="03efd-148">El comando `code` abre la carpeta *SignalRWebPack* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="03efd-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="03efd-149">Ejecute el siguiente comando de CLI de .NET Core en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="03efd-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="03efd-150">El comando anterior agrega el paquete [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/), que habilita la compilación de TypeScript en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="03efd-151">Configuración de Webpack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="03efd-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="03efd-152">Los pasos siguientes permiten configurar la conversión de TypeScript a JavaScript y la agrupación de los recursos del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="03efd-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="03efd-153">Ejecute el comando siguiente en la raíz del proyecto para crear un archivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="03efd-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="03efd-154">Agregue la propiedad resaltada al archivo *package.json* y guarde los cambios efectuados en el archivo:</span><span class="sxs-lookup"><span data-stu-id="03efd-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="03efd-155">Si establece la propiedad `private` en `true`, evitará las advertencias de la instalación de paquetes en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="03efd-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="03efd-156">Instale los paquetes npm necesarios.</span><span class="sxs-lookup"><span data-stu-id="03efd-156">Install the required npm packages.</span></span> <span data-ttu-id="03efd-157">Ejecute el comando siguiente desde la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="03efd-158">Algunos detalles del comando para tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="03efd-158">Some command details to note:</span></span>

    * <span data-ttu-id="03efd-159">En cada nombre de paquete, un número de versión sigue al signo `@`.</span><span class="sxs-lookup"><span data-stu-id="03efd-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="03efd-160">npm instala esas versiones de paquete específicas.</span><span class="sxs-lookup"><span data-stu-id="03efd-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="03efd-161">La opción `-E` deshabilita el comportamiento predeterminado de npm de escribir operadores de intervalo de [versionamiento semántico](https://semver.org/) en *package.json*.</span><span class="sxs-lookup"><span data-stu-id="03efd-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="03efd-162">Por ejemplo, se usa `"webpack": "4.41.5"` en lugar de `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="03efd-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="03efd-163">Esta opción impide actualizaciones no deseadas a versiones más recientes del paquete.</span><span class="sxs-lookup"><span data-stu-id="03efd-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="03efd-164">Consulte la documentación de [npm-install](https://docs.npmjs.com/cli/install) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="03efd-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="03efd-165">Reemplace la propiedad `scripts` del archivo *package.json* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="03efd-166">Más detalles sobre los scripts:</span><span class="sxs-lookup"><span data-stu-id="03efd-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="03efd-167">`build`: agrupa los recursos del lado cliente en modo de desarrollo y supervisa los cambios del archivo.</span><span class="sxs-lookup"><span data-stu-id="03efd-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="03efd-168">El monitor de archivos hace que la agrupación se vuelva a generar cada vez que cambia un archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="03efd-169">La opción `mode` deshabilita las optimizaciones de producción, como la agitación del árbol y la minificación.</span><span class="sxs-lookup"><span data-stu-id="03efd-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="03efd-170">Use `build` únicamente durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="03efd-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="03efd-171">`release`: agrupa los recursos del lado cliente en modo de producción.</span><span class="sxs-lookup"><span data-stu-id="03efd-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="03efd-172">`publish`: ejecuta el script `release` para agrupar los recursos del lado cliente en modo de producción.</span><span class="sxs-lookup"><span data-stu-id="03efd-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="03efd-173">Llama al comando [publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core para publicar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="03efd-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="03efd-174">Cree un archivo denominado *webpack.config.js*, en la raíz del proyecto, con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="03efd-175">El archivo anterior configura la compilación de Webpack.</span><span class="sxs-lookup"><span data-stu-id="03efd-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="03efd-176">Algunos detalles de configuración para tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="03efd-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="03efd-177">La propiedad `output` invalida el valor predeterminado de *dist*.</span><span class="sxs-lookup"><span data-stu-id="03efd-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="03efd-178">En su lugar, la agrupación se genera en el directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="03efd-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="03efd-179">La matriz `resolve.extensions` incluye *.js* para importar el código JavaScript del cliente de [SignalR.</span><span class="sxs-lookup"><span data-stu-id="03efd-179">The `resolve.extensions` array includes *.js* to import the [SignalR client JavaScript.</span></span>

1. <span data-ttu-id="03efd-180">Cree un *src* directorio en la raíz del proyecto para almacenar los recursos del lado cliente del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="03efd-181">Cree *src/index.html* con el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="03efd-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="03efd-182">El código HTML anterior define el marcado reutilizable de la página principal.</span><span class="sxs-lookup"><span data-stu-id="03efd-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="03efd-183">Cree un directorio *src/css*.</span><span class="sxs-lookup"><span data-stu-id="03efd-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="03efd-184">Su objetivo es almacenar los archivos *.css* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="03efd-185">Cree *src/css/main.css* con la especificación CSS siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="03efd-186">El archivo *main.css* anterior aplica estilo a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="03efd-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="03efd-187">Cree *src/tsconfig.json* con el JSON siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="03efd-188">El código anterior configura el compilador de TypeScript para generar JavaScript compatible con [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="03efd-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="03efd-189">Cree *src/index.ts* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="03efd-190">El elemento TypeScript anterior recupera las referencias a elementos DOM y adjunta dos controladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="03efd-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="03efd-191">`keyup`: este evento se desencadena cuando el usuario escribe algo en el cuadro de texto `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="03efd-192">La función `send` se llama cuando el usuario presiona la tecla **Entrar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="03efd-193">`click`: este evento se desencadena cuando el usuario hace clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="03efd-194">Se llama a la función `send`.</span><span class="sxs-lookup"><span data-stu-id="03efd-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="03efd-195">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="03efd-195">Configure the app</span></span>

1. <span data-ttu-id="03efd-196">En `Startup.Configure`, agregue llamadas a [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="03efd-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="03efd-197">El código anterior permite que el servidor busque y entregue el archivo *index.html*.</span><span class="sxs-lookup"><span data-stu-id="03efd-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="03efd-198">El archivo se entrega si el usuario escribe su dirección URL completa o la dirección URL raíz de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="03efd-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="03efd-199">Al final de `Startup.Configure`, asigne una ruta */hub* al centro `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="03efd-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="03efd-200">Reemplace el código que muestra *Hola mundo*</span><span class="sxs-lookup"><span data-stu-id="03efd-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="03efd-201">por la línea siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="03efd-202">En `Startup.ConfigureServices`, llame a [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="03efd-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="03efd-203">Cree un directorio denominado *Hubs* en la raíz del proyecto *SignalRWebPack/* para almacenar el centro [SignalR.</span><span class="sxs-lookup"><span data-stu-id="03efd-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the [SignalR hub.</span></span>

1. <span data-ttu-id="03efd-204">Cree el concentrador *Hubs/ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="03efd-205">Agregue la instrucción `using` siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="03efd-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="03efd-206">Habilitar la comunicación entre cliente y servidor</span><span class="sxs-lookup"><span data-stu-id="03efd-206">Enable client and server communication</span></span>

<span data-ttu-id="03efd-207">La aplicación actualmente muestra un formulario básico para enviar mensajes, pero aún no es funcional.</span><span class="sxs-lookup"><span data-stu-id="03efd-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="03efd-208">El servidor está escuchando en una ruta específica, pero no hace nada con los mensajes enviados.</span><span class="sxs-lookup"><span data-stu-id="03efd-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="03efd-209">Ejecute el comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="03efd-210">El comando anterior instala lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-210">The preceding command installs:</span></span>

     * <span data-ttu-id="03efd-211">El [cliente TypeScript de [SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite al cliente enviar mensajes al servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-211">The [[SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="03efd-212">Las definiciones de tipo de TypeScript para Node.js, que habilita la comprobación en tiempo de compilación de los tipos de Node.js.</span><span class="sxs-lookup"><span data-stu-id="03efd-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="03efd-213">Agregue el código resaltado al archivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="03efd-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="03efd-214">El código anterior admite la recepción de mensajes desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="03efd-215">La clase `HubConnectionBuilder` crea un generador para configurar la conexión al servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="03efd-216">La función `withUrl` configura la dirección URL del concentrador.</span><span class="sxs-lookup"><span data-stu-id="03efd-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="03efd-217">[SignalR permite el intercambio de mensajes entre un cliente y un servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-217">[SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="03efd-218">Cada mensaje tiene un nombre específico.</span><span class="sxs-lookup"><span data-stu-id="03efd-218">Each message has a specific name.</span></span> <span data-ttu-id="03efd-219">Por ejemplo, puede haber mensajes con el nombre `messageReceived` que pueden ejecutar la lógica responsable de mostrar el nuevo mensaje en la zona de mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="03efd-220">La escucha a un mensaje concreto se puede realizar mediante la función `on`.</span><span class="sxs-lookup"><span data-stu-id="03efd-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="03efd-221">Se puede escuchar cualquier número de nombres de mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="03efd-222">También se pueden pasar parámetros al mensaje, como el nombre del autor y el contenido del mensaje recibido.</span><span class="sxs-lookup"><span data-stu-id="03efd-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="03efd-223">Una vez que el cliente recibe un mensaje, se crea un elemento `div` con el nombre del autor y el contenido del mensaje en su atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="03efd-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="03efd-224">Se agrega al elemento `div` principal que muestra los mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="03efd-225">Ahora que el cliente puede recibir mensajes, debe configurarlo para poder enviarlos.</span><span class="sxs-lookup"><span data-stu-id="03efd-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="03efd-226">Agregue el código resaltado al archivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="03efd-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="03efd-227">El envío de mensajes a través de la conexión de WebSockets requiere llamar al método `send`.</span><span class="sxs-lookup"><span data-stu-id="03efd-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="03efd-228">El primer parámetro del método es el nombre del mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="03efd-229">Los datos del mensaje se encuentran en los otros parámetros.</span><span class="sxs-lookup"><span data-stu-id="03efd-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="03efd-230">En este ejemplo, se envía al servidor un mensaje identificado como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="03efd-231">El mensaje está formado por el nombre de usuario y la entrada del usuario desde un cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="03efd-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="03efd-232">Si el envío funciona, se borra el valor del cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="03efd-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="03efd-233">Agregue el método `NewMessage` a la clase `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="03efd-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="03efd-234">El código anterior difunde los mensajes recibidos a todos los usuarios conectados, una vez que el servidor los recibe.</span><span class="sxs-lookup"><span data-stu-id="03efd-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="03efd-235">No es necesario tener un método `on` genérico para recibir todos los mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="03efd-236">Basta con un método que tenga el nombre del mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="03efd-237">En este ejemplo, el cliente de TypeScript envía un mensaje que se identifica como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="03efd-238">El método `NewMessage` de C# espera los datos enviados por el cliente.</span><span class="sxs-lookup"><span data-stu-id="03efd-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="03efd-239">Se realiza una llamada a [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) de [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="03efd-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="03efd-240">Los mensajes recibidos se envían a todos los clientes conectados al concentrador.</span><span class="sxs-lookup"><span data-stu-id="03efd-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="03efd-241">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="03efd-241">Test the app</span></span>

<span data-ttu-id="03efd-242">Confirme que la aplicación funciona con los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="03efd-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="03efd-244">Ejecute Webpack en modo *release*.</span><span class="sxs-lookup"><span data-stu-id="03efd-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="03efd-245">Desde la ventana **Consola del Administrador de paquetes**, ejecute el comando siguiente en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="03efd-246">Si no está en la raíz del proyecto, escriba `cd SignalRWebPack` antes de introducir el comando.</span><span class="sxs-lookup"><span data-stu-id="03efd-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="03efd-247">Seleccione **Depurar** > **Iniciar sin depurar** para iniciar la aplicación en un explorador sin adjuntar el depurador.</span><span class="sxs-lookup"><span data-stu-id="03efd-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="03efd-248">El archivo *wwwroot/index.html* se entrega en `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="03efd-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="03efd-249">Si obtiene errores de compilación, intente cerrar y volver a abrir la solución.</span><span class="sxs-lookup"><span data-stu-id="03efd-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="03efd-250">Abra otra instancia del explorador (sirve cualquiera).</span><span class="sxs-lookup"><span data-stu-id="03efd-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="03efd-251">Pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="03efd-252">Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="03efd-253">El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="03efd-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="03efd-255">Ejecute Webpack en modo *release* mediante la ejecución del comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="03efd-256">Compile y ejecute la aplicación mediante la ejecución del comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="03efd-257">El servidor web inicia la aplicación y hace que esté disponible en el host local.</span><span class="sxs-lookup"><span data-stu-id="03efd-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="03efd-258">Abra un explorador en `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="03efd-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="03efd-259">Se entrega el archivo *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="03efd-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="03efd-260">Copie la dirección URL de la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="03efd-261">Abra otra instancia del explorador (sirve cualquiera).</span><span class="sxs-lookup"><span data-stu-id="03efd-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="03efd-262">Pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="03efd-263">Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="03efd-264">El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="03efd-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensaje mostrado en las dos ventanas del explorador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="03efd-266">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="03efd-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="03efd-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="03efd-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="03efd-269">.NET Core SDK 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="03efd-270">[Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="03efd-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="03efd-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="03efd-273">.NET Core SDK 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="03efd-274">C# para Visual Studio Code versión 1.17.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="03efd-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="03efd-275">[Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="03efd-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="03efd-276">Creación de la aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03efd-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03efd-278">Configure Visual Studio para buscar npm en la variable de entorno *PATH*.</span><span class="sxs-lookup"><span data-stu-id="03efd-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="03efd-279">De forma predeterminada, Visual Studio usa la versión de npm que se encuentra en su directorio de instalación.</span><span class="sxs-lookup"><span data-stu-id="03efd-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="03efd-280">Siga estas instrucciones en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="03efd-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="03efd-281">Vaya a **Herramientas** > **Opciones** > **Proyectos y soluciones** > **Administración de paquetes web** > **Herramientas web externas**.</span><span class="sxs-lookup"><span data-stu-id="03efd-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="03efd-282">Seleccione la entrada *$(PATH)* en la lista.</span><span class="sxs-lookup"><span data-stu-id="03efd-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="03efd-283">Haga clic en la flecha arriba para mover la entrada a la segunda posición de la lista.</span><span class="sxs-lookup"><span data-stu-id="03efd-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuración de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="03efd-285">Se ha completado la configuración de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03efd-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="03efd-286">Es el momento de crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-286">It's time to create the project.</span></span>

1. <span data-ttu-id="03efd-287">Use la opción de menú **Archivo** > **Nuevo** > **Proyecto** y seleccione la plantilla **Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="03efd-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="03efd-288">Asigne el nombre *SignalRWebPack* al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="03efd-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="03efd-289">Seleccione *.NET Core* en la lista desplegable de plataforma de destino y *ASP.NET Core 2.2* en la lista desplegable del selector de plataforma.</span><span class="sxs-lookup"><span data-stu-id="03efd-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="03efd-290">Seleccione la plantilla **Vacía** y **Crear**.</span><span class="sxs-lookup"><span data-stu-id="03efd-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03efd-292">Ejecute el comando siguiente en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="03efd-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="03efd-293">Se crea una aplicación web ASP.NET Core vacía, destinada a .NET Core, en el directorio *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="03efd-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="03efd-294">Configuración de Webpack y TypeScript</span><span class="sxs-lookup"><span data-stu-id="03efd-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="03efd-295">Los pasos siguientes permiten configurar la conversión de TypeScript a JavaScript y la agrupación de los recursos del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="03efd-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="03efd-296">Ejecute el comando siguiente en la raíz del proyecto para crear un archivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="03efd-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="03efd-297">Agregue la propiedad resaltada al archivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="03efd-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="03efd-298">Si establece la propiedad `private` en `true`, evitará las advertencias de la instalación de paquetes en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="03efd-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="03efd-299">Instale los paquetes npm necesarios.</span><span class="sxs-lookup"><span data-stu-id="03efd-299">Install the required npm packages.</span></span> <span data-ttu-id="03efd-300">Ejecute el comando siguiente desde la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="03efd-301">Algunos detalles del comando para tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="03efd-301">Some command details to note:</span></span>

    * <span data-ttu-id="03efd-302">En cada nombre de paquete, un número de versión sigue al signo `@`.</span><span class="sxs-lookup"><span data-stu-id="03efd-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="03efd-303">npm instala esas versiones de paquete específicas.</span><span class="sxs-lookup"><span data-stu-id="03efd-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="03efd-304">La opción `-E` deshabilita el comportamiento predeterminado de npm de escribir operadores de intervalo de [versionamiento semántico](https://semver.org/) en *package.json*.</span><span class="sxs-lookup"><span data-stu-id="03efd-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="03efd-305">Por ejemplo, se usa `"webpack": "4.29.3"` en lugar de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="03efd-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="03efd-306">Esta opción impide actualizaciones no deseadas a versiones más recientes del paquete.</span><span class="sxs-lookup"><span data-stu-id="03efd-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="03efd-307">Consulte la documentación de [npm-install](https://docs.npmjs.com/cli/install) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="03efd-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="03efd-308">Reemplace la propiedad `scripts` del archivo *package.json* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="03efd-309">Más detalles sobre los scripts:</span><span class="sxs-lookup"><span data-stu-id="03efd-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="03efd-310">`build`: agrupa los recursos del lado cliente en modo de desarrollo y supervisa los cambios del archivo.</span><span class="sxs-lookup"><span data-stu-id="03efd-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="03efd-311">El monitor de archivos hace que la agrupación se vuelva a generar cada vez que cambia un archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="03efd-312">La opción `mode` deshabilita las optimizaciones de producción, como la agitación del árbol y la minificación.</span><span class="sxs-lookup"><span data-stu-id="03efd-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="03efd-313">Use `build` únicamente durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="03efd-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="03efd-314">`release`: agrupa los recursos del lado cliente en modo de producción.</span><span class="sxs-lookup"><span data-stu-id="03efd-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="03efd-315">`publish`: ejecuta el script `release` para agrupar los recursos del lado cliente en modo de producción.</span><span class="sxs-lookup"><span data-stu-id="03efd-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="03efd-316">Llama al comando [publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core para publicar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="03efd-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="03efd-317">Cree un archivo denominado *webpack.config.js*, en la raíz del proyecto, con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="03efd-318">El archivo anterior configura la compilación de Webpack.</span><span class="sxs-lookup"><span data-stu-id="03efd-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="03efd-319">Algunos detalles de configuración para tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="03efd-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="03efd-320">La propiedad `output` invalida el valor predeterminado de *dist*.</span><span class="sxs-lookup"><span data-stu-id="03efd-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="03efd-321">En su lugar, la agrupación se genera en el directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="03efd-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="03efd-322">La matriz `resolve.extensions` incluye *.js* para importar el código JavaScript del cliente de [SignalR.</span><span class="sxs-lookup"><span data-stu-id="03efd-322">The `resolve.extensions` array includes *.js* to import the [SignalR client JavaScript.</span></span>

1. <span data-ttu-id="03efd-323">Cree un *src* directorio en la raíz del proyecto para almacenar los recursos del lado cliente del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="03efd-324">Cree *src/index.html* con el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="03efd-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="03efd-325">El código HTML anterior define el marcado reutilizable de la página principal.</span><span class="sxs-lookup"><span data-stu-id="03efd-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="03efd-326">Cree un directorio *src/css*.</span><span class="sxs-lookup"><span data-stu-id="03efd-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="03efd-327">Su objetivo es almacenar los archivos *.css* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="03efd-328">Cree *src/css/main.css* con el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="03efd-329">El archivo *main.css* anterior aplica estilo a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="03efd-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="03efd-330">Cree *src/tsconfig.json* con el JSON siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="03efd-331">El código anterior configura el compilador de TypeScript para generar JavaScript compatible con [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="03efd-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="03efd-332">Cree *src/index.ts* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="03efd-333">El elemento TypeScript anterior recupera las referencias a elementos DOM y adjunta dos controladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="03efd-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="03efd-334">`keyup`: este evento se desencadena cuando el usuario escribe en el cuadro de texto `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="03efd-335">La función `send` se llama cuando el usuario presiona la tecla **Entrar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="03efd-336">`click`: este evento se desencadena cuando el usuario hace clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="03efd-337">Se llama a la función `send`.</span><span class="sxs-lookup"><span data-stu-id="03efd-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="03efd-338">Configuración de la aplicación ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03efd-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="03efd-339">El código proporcionado en el método `Startup.Configure` muestra *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="03efd-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="03efd-340">Reemplace la llamada al método `app.Run` por las llamadas a [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="03efd-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="03efd-341">El código anterior permite que el servidor busque y proporcione el archivo *index.html*, con independencia de que el usuario escriba su dirección URL completa o la dirección URL raíz de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="03efd-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="03efd-342">Llame a [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="03efd-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03efd-343">Esta acción agrega los servicios [SignalR al proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-343">It adds the [SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="03efd-344">Asigne una ruta */hub* al concentrador `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="03efd-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="03efd-345">Agregue la línea siguiente al final de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="03efd-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="03efd-346">Cree un directorio denominado *Hubs* en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="03efd-347">Su objetivo es almacenar el concentrador de [SignalR, que se crea en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="03efd-347">Its purpose is to store the [SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="03efd-348">Cree el concentrador *Hubs/ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="03efd-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="03efd-349">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="03efd-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="03efd-350">Habilitar la comunicación entre cliente y servidor</span><span class="sxs-lookup"><span data-stu-id="03efd-350">Enable client and server communication</span></span>

<span data-ttu-id="03efd-351">Actualmente, en la aplicación se muestra un formulario simple para enviar mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="03efd-352">Al intentar hacer algo no sucede nada.</span><span class="sxs-lookup"><span data-stu-id="03efd-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="03efd-353">El servidor está escuchando en una ruta específica, pero no hace nada con los mensajes enviados.</span><span class="sxs-lookup"><span data-stu-id="03efd-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="03efd-354">Ejecute el comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="03efd-355">El comando anterior instala el [cliente TypeScript de [SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite al cliente enviar mensajes al servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-355">The preceding command installs the [[SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="03efd-356">Agregue el código resaltado al archivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="03efd-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="03efd-357">El código anterior admite la recepción de mensajes desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="03efd-358">La clase `HubConnectionBuilder` crea un generador para configurar la conexión al servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="03efd-359">La función `withUrl` configura la dirección URL del concentrador.</span><span class="sxs-lookup"><span data-stu-id="03efd-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="03efd-360">[SignalR permite el intercambio de mensajes entre un cliente y un servidor.</span><span class="sxs-lookup"><span data-stu-id="03efd-360">[SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="03efd-361">Cada mensaje tiene un nombre específico.</span><span class="sxs-lookup"><span data-stu-id="03efd-361">Each message has a specific name.</span></span> <span data-ttu-id="03efd-362">Por ejemplo, puede haber mensajes con el nombre `messageReceived` que pueden ejecutar la lógica responsable de mostrar el nuevo mensaje en la zona de mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="03efd-363">La escucha a un mensaje concreto se puede realizar mediante la función `on`.</span><span class="sxs-lookup"><span data-stu-id="03efd-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="03efd-364">Puede escuchar a cualquier número de nombres de mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-364">You can listen to any number of message names.</span></span> <span data-ttu-id="03efd-365">También se pueden pasar parámetros al mensaje, como el nombre del autor y el contenido del mensaje recibido.</span><span class="sxs-lookup"><span data-stu-id="03efd-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="03efd-366">Una vez que el cliente recibe un mensaje, se crea un elemento `div` con el nombre del autor y el contenido del mensaje en su atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="03efd-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="03efd-367">El nuevo mensaje se agrega al elemento `div` principal que muestra los mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="03efd-368">Ahora que el cliente puede recibir mensajes, debe configurarlo para poder enviarlos.</span><span class="sxs-lookup"><span data-stu-id="03efd-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="03efd-369">Agregue el código resaltado al archivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="03efd-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="03efd-370">El envío de mensajes a través de la conexión de WebSockets requiere llamar al método `send`.</span><span class="sxs-lookup"><span data-stu-id="03efd-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="03efd-371">El primer parámetro del método es el nombre del mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="03efd-372">Los datos del mensaje se encuentran en los otros parámetros.</span><span class="sxs-lookup"><span data-stu-id="03efd-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="03efd-373">En este ejemplo, se envía al servidor un mensaje identificado como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="03efd-374">El mensaje está formado por el nombre de usuario y la entrada del usuario desde un cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="03efd-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="03efd-375">Si el envío funciona, se borra el valor del cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="03efd-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="03efd-376">Agregue el método `NewMessage` a la clase `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="03efd-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="03efd-377">El código anterior difunde los mensajes recibidos a todos los usuarios conectados, una vez que el servidor los recibe.</span><span class="sxs-lookup"><span data-stu-id="03efd-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="03efd-378">No es necesario tener un método `on` genérico para recibir todos los mensajes.</span><span class="sxs-lookup"><span data-stu-id="03efd-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="03efd-379">Basta con un método que tenga el nombre del mensaje.</span><span class="sxs-lookup"><span data-stu-id="03efd-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="03efd-380">En este ejemplo, el cliente de TypeScript envía un mensaje que se identifica como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="03efd-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="03efd-381">El método `NewMessage` de C# espera los datos enviados por el cliente.</span><span class="sxs-lookup"><span data-stu-id="03efd-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="03efd-382">Se realiza una llamada a [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) de [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="03efd-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="03efd-383">Los mensajes recibidos se envían a todos los clientes conectados al concentrador.</span><span class="sxs-lookup"><span data-stu-id="03efd-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="03efd-384">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="03efd-384">Test the app</span></span>

<span data-ttu-id="03efd-385">Confirme que la aplicación funciona con los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="03efd-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03efd-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03efd-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="03efd-387">Ejecute Webpack en modo *release*.</span><span class="sxs-lookup"><span data-stu-id="03efd-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="03efd-388">Desde la ventana **Consola del Administrador de paquetes**, ejecute el comando siguiente en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="03efd-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="03efd-389">Si no está en la raíz del proyecto, escriba `cd SignalRWebPack` antes de introducir el comando.</span><span class="sxs-lookup"><span data-stu-id="03efd-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="03efd-390">Seleccione **Depurar** > **Iniciar sin depurar** para iniciar la aplicación en un explorador sin adjuntar el depurador.</span><span class="sxs-lookup"><span data-stu-id="03efd-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="03efd-391">El archivo *wwwroot/index.html* se entrega en `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="03efd-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="03efd-392">Abra otra instancia del explorador (sirve cualquiera).</span><span class="sxs-lookup"><span data-stu-id="03efd-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="03efd-393">Pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="03efd-394">Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="03efd-395">El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="03efd-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03efd-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03efd-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="03efd-397">Ejecute Webpack en modo *release* mediante la ejecución del comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="03efd-398">Compile y ejecute la aplicación mediante la ejecución del comando siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="03efd-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="03efd-399">El servidor web inicia la aplicación y hace que esté disponible en el host local.</span><span class="sxs-lookup"><span data-stu-id="03efd-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="03efd-400">Abra un explorador en `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="03efd-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="03efd-401">Se entrega el archivo *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="03efd-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="03efd-402">Copie la dirección URL de la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="03efd-403">Abra otra instancia del explorador (sirve cualquiera).</span><span class="sxs-lookup"><span data-stu-id="03efd-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="03efd-404">Pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="03efd-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="03efd-405">Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="03efd-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="03efd-406">El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="03efd-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensaje mostrado en las dos ventanas del explorador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="03efd-408">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="03efd-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
