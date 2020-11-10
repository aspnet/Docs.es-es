---
title: Perfiles de publicación (.pubxml) de Visual Studio para la implementación de aplicaciones ASP.NET Core
author: rick-anderson
description: Aprenda a crear perfiles de publicación en Visual Studio y usarlos para administrar implementaciones de aplicaciones ASP.NET Core en diversos destinos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 98dfd539171807cbf94d4ac8746458152c809495
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057575"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="22c27-103">Perfiles de publicación (.pubxml) de Visual Studio para la implementación de aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22c27-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="22c27-104">Por [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="22c27-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="22c27-105">Este documento se centra en el uso de Visual Studio 2019 o versiones posteriores para crear y usar perfiles de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="22c27-106">Los perfiles de publicación creados con Visual Studio se pueden usar con MSBuild y Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22c27-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="22c27-107">Para obtener instrucciones sobre la publicación en Azure, consulte <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="22c27-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="22c27-108">El comando `dotnet new mvc` produce un archivo de proyecto que contiene el siguiente [elemento \<Project> de nivel de raíz](/visualstudio/msbuild/project-element-msbuild):</span><span class="sxs-lookup"><span data-stu-id="22c27-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="22c27-109">El atributo `Sdk` del elemento anterior `<Project>` importa las [propiedades](/visualstudio/msbuild/msbuild-properties) y [objetivos](/visualstudio/msbuild/msbuild-targets) de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* y *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* , respectivamente.</span><span class="sxs-lookup"><span data-stu-id="22c27-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* , respectively.</span></span> <span data-ttu-id="22c27-110">La ubicación predeterminada de `$(MSBuildSDKsPath)` (con Visual Studio 2019 Enterprise) es la carpeta *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="22c27-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="22c27-111">`Microsoft.NET.Sdk.Web` ([SDK web](xref:razor-pages/web-sdk)) depende de otros SDK, como `Microsoft.NET.Sdk` ([SDK de .NET Core](/dotnet/core/project-sdk/msbuild-props)) y `Microsoft.NET.Sdk.:::no-loc(Razor):::` ([SDK de :::no-loc(Razor):::](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="22c27-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.:::no-loc(Razor):::` ([:::no-loc(Razor)::: SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="22c27-112">Se importan las propiedades y objetivos de MSBuild asociados con cada SDK dependiente.</span><span class="sxs-lookup"><span data-stu-id="22c27-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="22c27-113">Los destinos de publicación importan el conjunto adecuado de destinos en función del método de publicación usado.</span><span class="sxs-lookup"><span data-stu-id="22c27-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="22c27-114">Cuando se carga un proyecto en Visual Studio o MSBuild, se llevan a cabo las siguientes acciones generales:</span><span class="sxs-lookup"><span data-stu-id="22c27-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="22c27-115">Compilación del proyecto</span><span class="sxs-lookup"><span data-stu-id="22c27-115">Build project</span></span>
* <span data-ttu-id="22c27-116">Cálculo de los archivos para la publicación</span><span class="sxs-lookup"><span data-stu-id="22c27-116">Compute files to publish</span></span>
* <span data-ttu-id="22c27-117">Publicación de los archivos en el destino</span><span class="sxs-lookup"><span data-stu-id="22c27-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="22c27-118">Cálculo de los elementos del proyecto</span><span class="sxs-lookup"><span data-stu-id="22c27-118">Compute project items</span></span>

<span data-ttu-id="22c27-119">Cuando se carga el proyecto, se procesan los [elementos del proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (archivos).</span><span class="sxs-lookup"><span data-stu-id="22c27-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="22c27-120">El tipo de elemento determina cómo se procesa el archivo.</span><span class="sxs-lookup"><span data-stu-id="22c27-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="22c27-121">De forma predeterminada, los archivos *.cs* se incluyen en la lista de elementos `Compile`.</span><span class="sxs-lookup"><span data-stu-id="22c27-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="22c27-122">Después se compilan los archivos de la lista de elementos `Compile`.</span><span class="sxs-lookup"><span data-stu-id="22c27-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="22c27-123">La lista de elementos `Content` contiene archivos que se van a publicar, además de los resultados de compilación.</span><span class="sxs-lookup"><span data-stu-id="22c27-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="22c27-124">De forma predeterminada, los archivos que coinciden con los patrones `wwwroot\**`, `**\*.config` y `**\*.json` se incluyen en la lista de elementos `Content`.</span><span class="sxs-lookup"><span data-stu-id="22c27-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="22c27-125">Por ejemplo, el [patrón global](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` coincide con los archivos de la carpeta *wwwroot* y de sus subcarpetas.</span><span class="sxs-lookup"><span data-stu-id="22c27-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22c27-126">El [SDK web](xref:razor-pages/web-sdk) importa el [SDK de :::no-loc(Razor):::](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="22c27-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="22c27-127">Como resultado, los archivos que coinciden con los patrones `**\*.cshtml` y `**\*.razor` se incluyen también en la lista de elementos `Content`.</span><span class="sxs-lookup"><span data-stu-id="22c27-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="22c27-128">El [SDK web](xref:razor-pages/web-sdk) importa el [SDK de :::no-loc(Razor):::](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="22c27-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="22c27-129">Como resultado, los archivos que coinciden con el patrón `**\*.cshtml` y se incluye también en la lista de elementos `Content`.</span><span class="sxs-lookup"><span data-stu-id="22c27-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="22c27-130">Para agregar explícitamente un archivo a la lista de publicación, agregue el archivo directamente en el archivo *.csproj* , como se muestra en la sección [Archivos de inclusión](#include-files).</span><span class="sxs-lookup"><span data-stu-id="22c27-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="22c27-131">Al seleccionar el botón **Publicar** en Visual Studio o al publicar desde la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="22c27-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="22c27-132">Se calculan los elementos/propiedades (los archivos que se deben compilar).</span><span class="sxs-lookup"><span data-stu-id="22c27-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="22c27-133">**Solo Visual Studio** : Se han restaurado los paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="22c27-133">**Visual Studio only** : NuGet packages are restored.</span></span> <span data-ttu-id="22c27-134">(la restauración debe ser explícita por parte del usuario en la CLI).</span><span class="sxs-lookup"><span data-stu-id="22c27-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="22c27-135">Se compila el proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-135">The project builds.</span></span>
* <span data-ttu-id="22c27-136">Se calculan los elementos de publicación (los archivos que se deben publicar).</span><span class="sxs-lookup"><span data-stu-id="22c27-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="22c27-137">Se publica el proyecto (los archivos calculados se copian en el destino de publicación).</span><span class="sxs-lookup"><span data-stu-id="22c27-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="22c27-138">Cuando hace referencia a un proyecto de ASP.NET Core `Microsoft.NET.Sdk.Web` en el archivo de proyecto, se coloca un archivo *app_offline.htm* en la raíz del directorio de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="22c27-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="22c27-139">Cuando el archivo está presente, el módulo de ASP.NET Core cierra correctamente la aplicación y proporciona el archivo *app_offline.htm* durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="22c27-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="22c27-140">Para más información, vea [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm) (Referencia de configuración del módulo de ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="22c27-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="22c27-141">Publicación básica de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="22c27-141">Basic command-line publishing</span></span>

<span data-ttu-id="22c27-142">La publicación de línea de comandos funciona en todas las plataformas admitidas de .NET Core y no se requiere Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22c27-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="22c27-143">En los ejemplos siguientes, se ejecuta el comando [dotnet publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core desde el directorio del proyecto (que contiene el archivo *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="22c27-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="22c27-144">Si la carpeta del proyecto no es el directorio de trabajo actual, pase de forma explícita la ruta de acceso del archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="22c27-145">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="22c27-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="22c27-146">Ejecute los comandos siguientes para crear y publicar una aplicación web:</span><span class="sxs-lookup"><span data-stu-id="22c27-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="22c27-147">Con el comando `dotnet publish` se genera una variación de la salida siguiente:</span><span class="sxs-lookup"><span data-stu-id="22c27-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="22c27-148">El formato de la carpeta de publicación predeterminado es *bin\Debug\\{MONIKER DE LA PLATAFORMA DE DESTINO}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="22c27-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="22c27-149">Por ejemplo, *bin\Debug\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="22c27-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="22c27-150">El siguiente comando especifica una compilación `Release` y el directorio de publicación:</span><span class="sxs-lookup"><span data-stu-id="22c27-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="22c27-151">Con el comando `dotnet publish` se llama a MSBuild, lo que invoca el destino `Publish`.</span><span class="sxs-lookup"><span data-stu-id="22c27-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="22c27-152">Todos los parámetros pasados a `dotnet publish` se pasan a MSBuild.</span><span class="sxs-lookup"><span data-stu-id="22c27-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="22c27-153">Los parámetros `-c` y `-o` se asignan respectivamente a las propiedades `Configuration` y `OutputPath` de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="22c27-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="22c27-154">Se pueden pasar propiedades de MSBuild mediante cualquiera de los siguientes formatos:</span><span class="sxs-lookup"><span data-stu-id="22c27-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="22c27-155">Por ejemplo, el comando siguiente publica una compilación `Release` en un recurso compartido de red.</span><span class="sxs-lookup"><span data-stu-id="22c27-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="22c27-156">El recurso compartido de red se especifica con barras diagonales ( *//r8/* ) y funciona en todas las plataformas compatibles de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="22c27-156">The network share is specified with forward slashes ( *//r8/* ) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="22c27-157">Confirme que la aplicación publicada para la implementación no se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="22c27-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="22c27-158">Los archivos de la carpeta *publish* quedan bloqueados mientras se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="22c27-159">No se puede llevar a cabo la implementación porque no se pueden copiar los archivos bloqueados.</span><span class="sxs-lookup"><span data-stu-id="22c27-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="22c27-160">Publicar los perfiles</span><span class="sxs-lookup"><span data-stu-id="22c27-160">Publish profiles</span></span>

<span data-ttu-id="22c27-161">En esta sección se usa Visual Studio 2019 o versiones posteriores para crear un perfil de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="22c27-162">Una vez creado el perfil, es posible realizar la publicación desde Visual Studio o la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="22c27-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="22c27-163">Los perfiles de publicación pueden simplificar el proceso de publicación, y puede existir cualquier número de perfiles.</span><span class="sxs-lookup"><span data-stu-id="22c27-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="22c27-164">Cree un perfil de publicación en Visual Studio mediante la selección de una de las rutas de acceso siguientes:</span><span class="sxs-lookup"><span data-stu-id="22c27-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="22c27-165">Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones** , y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="22c27-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="22c27-166">Seleccione **Publicar {NOMBRE DEL PROYECTO}** en el menú **Compilar**.</span><span class="sxs-lookup"><span data-stu-id="22c27-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="22c27-167">Aparece la pestaña **Publicar** de la página de funcionalidades de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="22c27-168">Si el proyecto no contiene ningún perfil de publicación, se muestra la página **Elegir un destino de publicación**.</span><span class="sxs-lookup"><span data-stu-id="22c27-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="22c27-169">Se le pide que seleccione uno de los siguientes destinos de publicación:</span><span class="sxs-lookup"><span data-stu-id="22c27-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="22c27-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="22c27-170">Azure App Service</span></span>
* <span data-ttu-id="22c27-171">Azure App Service en Linux</span><span class="sxs-lookup"><span data-stu-id="22c27-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="22c27-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="22c27-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="22c27-173">Carpeta</span><span class="sxs-lookup"><span data-stu-id="22c27-173">Folder</span></span>
* <span data-ttu-id="22c27-174">IIS, FTP, Web Deploy (para cualquier servidor web)</span><span class="sxs-lookup"><span data-stu-id="22c27-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="22c27-175">Perfil de importación</span><span class="sxs-lookup"><span data-stu-id="22c27-175">Import Profile</span></span>

<span data-ttu-id="22c27-176">Para determinar el destino de publicación más adecuado, consulte [Qué opciones de publicación son las adecuadas para mí](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="22c27-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="22c27-177">Cuando seleccione el destino de publicación **Carpeta** , especifique una ruta de acceso a la carpeta para almacenar los recursos publicados.</span><span class="sxs-lookup"><span data-stu-id="22c27-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="22c27-178">La ruta de carpeta predeterminada es *bin\\{CONFIGURACIÓN DEL PROYECTO}\\{MONIKER DE LA PLATAFORMA DE DESTINO}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="22c27-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="22c27-179">Por ejemplo, *bin\Release\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="22c27-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="22c27-180">Seleccione el botón **Crear perfil** para terminar.</span><span class="sxs-lookup"><span data-stu-id="22c27-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="22c27-181">Una vez que se crea un perfil de publicación, cambia el contenido de la pestaña **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="22c27-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="22c27-182">El perfil recién creado aparece en una lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="22c27-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="22c27-183">Debajo de la lista desplegable, seleccione **Crear nuevo perfil** para crear otro perfil nuevo.</span><span class="sxs-lookup"><span data-stu-id="22c27-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="22c27-184">La herramienta de publicación de Visual Studio genera un archivo MSBuild denominado *Properties/PublishProfiles/{NOMBRE DEL PERFIL}.pubxml* en el que se describe el perfil de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="22c27-185">El archivo *.pubxml* :</span><span class="sxs-lookup"><span data-stu-id="22c27-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="22c27-186">Contiene los valores de la configuración de publicación y es utilizado por el proceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="22c27-187">Se puede modificar para personalizar el proceso de compilación y publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="22c27-188">Al publicar en un destino de Azure, el archivo *.pubxml* contiene el identificador de suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="22c27-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="22c27-189">Con ese tipo de destino, no se recomienda agregar este archivo al control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="22c27-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="22c27-190">Al publicar en un destino que no sea Azure, es seguro insertar el archivo *.pubxml* en el repositorio.</span><span class="sxs-lookup"><span data-stu-id="22c27-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="22c27-191">La información confidencial (por ejemplo, la contraseña de publicación) se cifra en cada usuario y máquina.</span><span class="sxs-lookup"><span data-stu-id="22c27-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="22c27-192">Este se almacena en el archivo *Properties/PublishProfiles/{NOMBRE DEL PERFIL}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="22c27-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="22c27-193">Como este archivo puede contener información confidencial, no se debe insertar en el repositorio de control del código fuente.</span><span class="sxs-lookup"><span data-stu-id="22c27-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="22c27-194">Para obtener información general sobre cómo publicar una aplicación web ASP.NET Core, vea <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="22c27-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="22c27-195">Las tareas y los destinos de MSBuild necesarios para publicar una aplicación web de ASP.NET Core son de código abierto en el repositorio [dotnet/websdk](https://github.com/dotnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="22c27-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="22c27-196">Los siguientes comandos pueden usar perfiles de publicación de carpeta, MSDeploy y [Kudu](https://github.com/projectkudu/kudu/wiki).</span><span class="sxs-lookup"><span data-stu-id="22c27-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="22c27-197">Ya que MSDeploy no tiene compatibilidad multiplataforma, las siguientes opciones de MSDeploy solo se admiten en Windows.</span><span class="sxs-lookup"><span data-stu-id="22c27-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="22c27-198">**Carpeta (funciona entre plataformas):**</span><span class="sxs-lookup"><span data-stu-id="22c27-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="22c27-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="22c27-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="22c27-200">**Paquete MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="22c27-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="22c27-201">En los ejemplos anteriores:</span><span class="sxs-lookup"><span data-stu-id="22c27-201">In the preceding examples:</span></span>

* <span data-ttu-id="22c27-202">`dotnet publish` y `dotnet build` admiten API de Kudu para publicar en Azure desde cualquier plataforma.</span><span class="sxs-lookup"><span data-stu-id="22c27-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="22c27-203">La publicación de Visual Studio admite API de Kudu, pero es compatible con WebSDK para la publicación multiplataforma en Azure.</span><span class="sxs-lookup"><span data-stu-id="22c27-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="22c27-204">No pase `DeployOnBuild` al comando `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="22c27-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="22c27-205">Para más información, consulte [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="22c27-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="22c27-206">Agregue un perfil de publicación a la carpeta del proyecto *Properties/PublishProfiles* con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="22c27-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="22c27-207">Ejemplo de publicación de carpetas</span><span class="sxs-lookup"><span data-stu-id="22c27-207">Folder publish example</span></span>

<span data-ttu-id="22c27-208">Al publicar con un perfil llamado *FolderProfile* , use cualquiera de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="22c27-208">When publishing with a profile named *FolderProfile* , use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="22c27-209">Con el comando [dotnet build](/dotnet/core/tools/dotnet-build) de la CLI de .NET Core se llama a `msbuild` para ejecutar el proceso de compilación y publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="22c27-210">Los comandos `dotnet build` y `msbuild` son equivalentes cuando se pasa un perfil de carpeta.</span><span class="sxs-lookup"><span data-stu-id="22c27-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="22c27-211">Al llamar a `msbuild` directamente en Windows, se usa la versión de .NET Framework de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="22c27-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="22c27-212">Llamada a `dotnet build` en un perfil que no sea de carpeta:</span><span class="sxs-lookup"><span data-stu-id="22c27-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="22c27-213">Invoca `msbuild`, que usa MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="22c27-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="22c27-214">Produce un error (incluso cuando se ejecuta en Windows).</span><span class="sxs-lookup"><span data-stu-id="22c27-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="22c27-215">Para publicar con un perfil que no es de carpeta, llame directamente a `msbuild`.</span><span class="sxs-lookup"><span data-stu-id="22c27-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="22c27-216">El siguiente perfil de publicación de carpeta se creó con Visual Studio y se publica en un recurso compartido de red:</span><span class="sxs-lookup"><span data-stu-id="22c27-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="22c27-217">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="22c27-217">In the preceding example:</span></span>

* <span data-ttu-id="22c27-218">La propiedad `<ExcludeApp_Data>` está presente simplemente para satisfacer un requisito de esquema XML.</span><span class="sxs-lookup"><span data-stu-id="22c27-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="22c27-219">La propiedad `<ExcludeApp_Data>` no tiene ningún efecto en el proceso de publicación, aunque haya una carpeta *App_Data* en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="22c27-220">La carpeta *App_Data* no recibe un tratamiento especial, como sí sucede en los proyectos de ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="22c27-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="22c27-221">La propiedad `<LastUsedBuildConfiguration>` se establece en `Release`.</span><span class="sxs-lookup"><span data-stu-id="22c27-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="22c27-222">Al efectuar una publicación en Visual Studio, el valor de `<LastUsedBuildConfiguration>` se establece con el valor cuando se inicia el proceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="22c27-223">`<LastUsedBuildConfiguration>` es especial y no se debe reemplazar en un archivo importado de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="22c27-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="22c27-224">Sin embargo, esta propiedad se puede invalidar desde la línea de comandos mediante uno de los métodos siguientes.</span><span class="sxs-lookup"><span data-stu-id="22c27-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="22c27-225">Con la CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="22c27-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="22c27-226">Con MSBuild:</span><span class="sxs-lookup"><span data-stu-id="22c27-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="22c27-227">Vea [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (MSBuild: Cómo establecer la propiedad de configuración).</span><span class="sxs-lookup"><span data-stu-id="22c27-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="22c27-228">Publicar en un punto de conexión de MSDeploy desde la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="22c27-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="22c27-229">En el ejemplo siguiente se utiliza una aplicación web ASP.NET Core de ejemplo creada mediante Visual Studio y denominada *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="22c27-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="22c27-230">Se agrega un perfil de publicación de aplicaciones de Azure con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22c27-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="22c27-231">Para obtener más información sobre cómo crear un perfil, consulte la sección [Perfiles de publicación](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="22c27-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="22c27-232">Para implementar la aplicación con un perfil de publicación, ejecute el comando `msbuild` desde un **símbolo del sistema para desarrolladores** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22c27-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="22c27-233">El símbolo del sistema se encuentra disponible en la carpeta *Visual Studio* del menú **Inicio** , en la barra de tareas de Windows.</span><span class="sxs-lookup"><span data-stu-id="22c27-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="22c27-234">Para facilitar el acceso, puede agregar el símbolo del sistema al menú **Herramientas** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22c27-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="22c27-235">Para más información, consulte [Símbolo del sistema para desarrolladores de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="22c27-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="22c27-236">MSBuild usa la sintaxis de comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="22c27-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="22c27-237">`{PATH}`: ruta de acceso al archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="22c27-238">`{PROFILE}`: nombre del perfil de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="22c27-239">`{USERNAME}`: nombre de usuario de MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="22c27-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="22c27-240">`{USERNAME}` está en el perfil de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="22c27-241">`{PASSWORD}`: contraseña de MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="22c27-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="22c27-242">Obtenga el valor `{PASSWORD}` en el archivo *{PROFILE}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="22c27-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="22c27-243">Descargue el archivo *.PublishSettings* desde:</span><span class="sxs-lookup"><span data-stu-id="22c27-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="22c27-244">**Explorador de soluciones** : Seleccione **Ver** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="22c27-244">**Solution Explorer** : Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="22c27-245">Conéctese con su suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="22c27-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="22c27-246">Abra **App Services**.</span><span class="sxs-lookup"><span data-stu-id="22c27-246">Open **App Services**.</span></span> <span data-ttu-id="22c27-247">Haga clic con el botón derecho en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-247">Right-click the app.</span></span> <span data-ttu-id="22c27-248">Seleccione **Descargar perfil de publicación**.</span><span class="sxs-lookup"><span data-stu-id="22c27-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="22c27-249">Azure Portal: Haga clic en **Obtener perfil de publicación** , en el panel **Información general** de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="22c27-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="22c27-250">En el ejemplo siguiente se usa un perfil de publicación denominado *AzureWebApp - Web Deploy* :</span><span class="sxs-lookup"><span data-stu-id="22c27-250">The following example uses a publish profile named *AzureWebApp - Web Deploy* :</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="22c27-251">También se puede usar un perfil de publicación ejecutando el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) de la CLI de .NET Core en un shell de comandos de Windows:</span><span class="sxs-lookup"><span data-stu-id="22c27-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="22c27-252">El comando `dotnet msbuild` es multiplataforma y permite compilar aplicaciones ASP.NET Core en macOS y Linux.</span><span class="sxs-lookup"><span data-stu-id="22c27-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="22c27-253">Sin embargo, MSBuild en macOS y Linux no permite implementar una aplicación en Azure ni en otros puntos de conexión de MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="22c27-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="22c27-254">Establecimiento del entorno</span><span class="sxs-lookup"><span data-stu-id="22c27-254">Set the environment</span></span>

<span data-ttu-id="22c27-255">Incluya la propiedad `<EnvironmentName>` en el perfil de publicación ( *.pubxml* ) o el archivo del proyecto para configurar el [entorno](xref:fundamentals/environments) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="22c27-255">Include the `<EnvironmentName>` property in the publish profile ( *.pubxml* ) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="22c27-256">Si necesita transformaciones de *web.config* (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="22c27-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="22c27-257">Archivos de exclusión</span><span class="sxs-lookup"><span data-stu-id="22c27-257">Exclude files</span></span>

<span data-ttu-id="22c27-258">Al publicar aplicaciones web de ASP.NET Core, se incluyen los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="22c27-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="22c27-259">Artefactos de compilación</span><span class="sxs-lookup"><span data-stu-id="22c27-259">Build artifacts</span></span>
* <span data-ttu-id="22c27-260">Las carpetas y archivos que coinciden con los siguientes patrones globales de uso:</span><span class="sxs-lookup"><span data-stu-id="22c27-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="22c27-261">`**\*.config` (por ejemplo, *web.config* )</span><span class="sxs-lookup"><span data-stu-id="22c27-261">`**\*.config` (for example, *web.config* )</span></span>
  * <span data-ttu-id="22c27-262">`**\*.json` (por ejemplo, *:::no-loc(appsettings.json):::* )</span><span class="sxs-lookup"><span data-stu-id="22c27-262">`**\*.json` (for example, *:::no-loc(appsettings.json):::* )</span></span>
  * `wwwroot\**`

<span data-ttu-id="22c27-263">MSBuild admite los [patrones globales](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="22c27-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="22c27-264">Por ejemplo, el siguiente elemento `<Content>` suprime la copia de los archivos de texto ( *.txt* ) de la carpeta *wwwroot/content* y de sus subcarpetas:</span><span class="sxs-lookup"><span data-stu-id="22c27-264">For example, the following `<Content>` element suppresses the copying of text ( *.txt* ) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="22c27-265">El marcado anterior se puede agregar a un perfil de publicación o al archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="22c27-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="22c27-266">Si se agrega al archivo *.csproj* , la regla se agrega a todos los perfiles de publicación del proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="22c27-267">El siguiente elemento `<MsDeploySkipRules>` excluye todos los archivos de la carpeta *wwwroot/content* :</span><span class="sxs-lookup"><span data-stu-id="22c27-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="22c27-268">`<MsDeploySkipRules>` no eliminará del sitio de implementación los destinos *skip*.</span><span class="sxs-lookup"><span data-stu-id="22c27-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="22c27-269">Los archivos y carpetas destinados a `<Content>` se eliminan del sitio de implementación.</span><span class="sxs-lookup"><span data-stu-id="22c27-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="22c27-270">Por ejemplo, suponga que una aplicación web implementada tenía los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="22c27-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="22c27-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="22c27-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="22c27-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="22c27-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="22c27-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="22c27-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="22c27-274">Si se agregan los siguientes elementos `<MsDeploySkipRules>`, esos archivos no se eliminarán en el sitio de implementación.</span><span class="sxs-lookup"><span data-stu-id="22c27-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="22c27-275">Los elementos `<MsDeploySkipRules>` anteriores impiden que se implementen los archivos *omitidos*.</span><span class="sxs-lookup"><span data-stu-id="22c27-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="22c27-276">Una vez que se implementan esos archivos, no se eliminarán.</span><span class="sxs-lookup"><span data-stu-id="22c27-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="22c27-277">El siguiente elemento `<Content>` elimina los archivos de destino en el sitio de implementación:</span><span class="sxs-lookup"><span data-stu-id="22c27-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="22c27-278">El uso de la implementación de línea de comandos con el elemento `<Content>` anterior produce una variación de la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="22c27-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="22c27-279">Archivos de inclusión</span><span class="sxs-lookup"><span data-stu-id="22c27-279">Include files</span></span>

<span data-ttu-id="22c27-280">En la siguientes secciones se describen diferentes enfoques para la inclusión de archivos en el momento de la publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="22c27-281">En la sección [Inclusión de archivos general](#general-file-inclusion) se usa el elemento `DotNetPublishFiles`, que se proporciona mediante un archivo de destinos de publicación en el [SDK web](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="22c27-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="22c27-282">En la sección [Inclusión de archivos selectiva](#selective-file-inclusion) se usa el elemento `ResolvedFileToPublish`, que se proporciona mediante un archivo de destinos de publicación en el [SDK de .NET Core](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="22c27-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="22c27-283">Dado que el SDK web depende del SDK de .NET Core, se puede usar cualquier elemento en un proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22c27-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="22c27-284">Inclusión de archivos general</span><span class="sxs-lookup"><span data-stu-id="22c27-284">General file inclusion</span></span>

<span data-ttu-id="22c27-285">En el siguiente ejemplo, en el elemento `<ItemGroup>` se muestra cómo copiar una carpeta que se encuentra fuera del directorio del proyecto en una carpeta del sitio publicado.</span><span class="sxs-lookup"><span data-stu-id="22c27-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="22c27-286">Los archivos agregados al elemento `<ItemGroup>` del marcado siguiente se incluyen de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="22c27-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="22c27-287">El marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="22c27-287">The preceding markup:</span></span>

* <span data-ttu-id="22c27-288">Se puede agregar al archivo *.csproj* o al perfil de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="22c27-289">Si se agrega al archivo *.csproj* , se incluye en todos los perfiles de publicación del proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="22c27-290">Declara un elemento `_CustomFiles` para almacenar los archivos coincidentes con el patrón global del atributo `Include`.</span><span class="sxs-lookup"><span data-stu-id="22c27-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="22c27-291">La carpeta *images* a la que se hace referencia en el patrón se encuentra fuera del directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="22c27-292">Una [propiedad reservada](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominada `$(MSBuildProjectDirectory)`, se resuelve en la ruta de acceso absoluta del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="22c27-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="22c27-293">Proporciona una lista de los archivos para el elemento `DotNetPublishFiles`.</span><span class="sxs-lookup"><span data-stu-id="22c27-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="22c27-294">El elemento `<DestinationRelativePath>` de la lista aparece vacío de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="22c27-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="22c27-295">El valor predeterminado se reemplaza en el marcado y usa [metadatos de elementos conocidos](/visualstudio/msbuild/msbuild-well-known-item-metadata) como `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="22c27-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="22c27-296">El texto interno representa la carpeta *wwwroot/images* del sitio publicado.</span><span class="sxs-lookup"><span data-stu-id="22c27-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="22c27-297">Inclusión de archivos selectiva</span><span class="sxs-lookup"><span data-stu-id="22c27-297">Selective file inclusion</span></span>

<span data-ttu-id="22c27-298">En el marcado resaltado del siguiente ejemplo se muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="22c27-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="22c27-299">La copia un archivo que se encuentra fuera del proyecto en la carpeta *wwwroot* del sitio publicado.</span><span class="sxs-lookup"><span data-stu-id="22c27-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="22c27-300">Se mantiene el nombre del archivo *ReadMe2.md*.</span><span class="sxs-lookup"><span data-stu-id="22c27-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="22c27-301">La exclusión de la carpeta *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="22c27-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="22c27-302">La exclusión de *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="22c27-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="22c27-303">En el ejemplo anterior se usa el elemento `ResolvedFileToPublish`, cuyo comportamiento predeterminado consiste siempre en copiar los archivos proporcionados en el atributo `Include` en el sitio publicado.</span><span class="sxs-lookup"><span data-stu-id="22c27-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="22c27-304">Invalide el comportamiento predeterminado mediante la inclusión de un elemento secundario `<CopyToPublishDirectory>` con el texto interno de `Never` o `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="22c27-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="22c27-305">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="22c27-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="22c27-306">Para ver más ejemplos de implementación, consulte el [archivo Léame del repositorio de WebSDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span><span class="sxs-lookup"><span data-stu-id="22c27-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="22c27-307">Ejecutar un destino antes o después de la publicación</span><span class="sxs-lookup"><span data-stu-id="22c27-307">Run a target before or after publishing</span></span>

<span data-ttu-id="22c27-308">Los destinos `BeforePublish` y `AfterPublish` ejecutan un destino antes o después del destino de publicación.</span><span class="sxs-lookup"><span data-stu-id="22c27-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="22c27-309">Agregue los siguientes elementos al perfil de publicación para registrar mensajes de la consola antes y después de la publicación:</span><span class="sxs-lookup"><span data-stu-id="22c27-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="22c27-310">Publicación en un servidor mediante un certificado que no es de confianza</span><span class="sxs-lookup"><span data-stu-id="22c27-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="22c27-311">Agregue la propiedad `<AllowUntrustedCertificate>` con un valor de `True` al perfil de publicación:</span><span class="sxs-lookup"><span data-stu-id="22c27-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="22c27-312">Servicio Kudu</span><span class="sxs-lookup"><span data-stu-id="22c27-312">The Kudu service</span></span>

<span data-ttu-id="22c27-313">Para ver los archivos de la implementación de una aplicación web de Azure App Service, use el [servicio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="22c27-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="22c27-314">Anexe el token `scm` al nombre de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="22c27-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="22c27-315">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="22c27-315">For example:</span></span>

| <span data-ttu-id="22c27-316">Resolución</span><span class="sxs-lookup"><span data-stu-id="22c27-316">URL</span></span>                                    | <span data-ttu-id="22c27-317">Resultado</span><span class="sxs-lookup"><span data-stu-id="22c27-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="22c27-318">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="22c27-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="22c27-319">Servicio Kudu</span><span class="sxs-lookup"><span data-stu-id="22c27-319">Kudu service</span></span> |

<span data-ttu-id="22c27-320">Seleccione el elemento de menú [Consola de depuración](https://github.com/projectkudu/kudu/wiki/Kudu-console) para ver, editar, eliminar o agregar archivos.</span><span class="sxs-lookup"><span data-stu-id="22c27-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22c27-321">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="22c27-321">Additional resources</span></span>

* <span data-ttu-id="22c27-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica la implementación de aplicaciones web y sitios web en servidores de IIS.</span><span class="sxs-lookup"><span data-stu-id="22c27-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="22c27-323">[Repositorio de SDK web en GitHub](https://github.com/dotnet/websdk/issues): problemas de archivos y características de solicitud para la implementación.</span><span class="sxs-lookup"><span data-stu-id="22c27-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="22c27-324">Publicación de una aplicación web ASP.NET en una máquina virtual de Azure desde Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c27-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
