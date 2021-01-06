---
title: Desarrollar aplicaciones ASP.NET Core con un monitor de archivos
author: rick-anderson
description: Este tutorial muestra cómo instalar y usar la herramienta de monitor de archivos (dotnet watch) de la CLI de .NET Core en una aplicación de ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060045"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="2a3b8-103">Desarrollar aplicaciones ASP.NET Core con un monitor de archivos</span><span class="sxs-lookup"><span data-stu-id="2a3b8-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="2a3b8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="2a3b8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="2a3b8-105">`dotnet watch` es una herramienta que ejecuta un comando de la [CLI de .NET Core](/dotnet/core/tools) cuando se modifican los archivos de código fuente.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="2a3b8-106">Por ejemplo, un cambio en un archivo puede desencadenar una compilación, una ejecución de prueba o una implementación.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="2a3b8-107">En este tutorial usaremos una API web existente con dos puntos de conexión: uno que devuelve una suma y otro que devuelve un producto.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="2a3b8-108">El método Product contiene un error, que se ha corregido en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="2a3b8-109">Descargue la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="2a3b8-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="2a3b8-110">Consta de dos proyectos: *WebApp* (API web de ASP.NET Core) y *WebAppTests* (pruebas unitarias para la API web).</span><span class="sxs-lookup"><span data-stu-id="2a3b8-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="2a3b8-111">En un shell de comandos, desplácese hasta la carpeta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="2a3b8-112">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="2a3b8-113">Puede usar `dotnet run --project <PROJECT>` para especificar un proyecto para que se ejecute.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="2a3b8-114">Por ejemplo, al ejecutar `dotnet run --project WebApp` desde la raíz de la aplicación de ejemplo, también se ejecutará el proyecto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="2a3b8-115">La salida de la consola muestra mensajes similares al siguiente (indicando que la aplicación se ejecuta y espera solicitudes):</span><span class="sxs-lookup"><span data-stu-id="2a3b8-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="2a3b8-116">En un explorador web, vaya a `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="2a3b8-117">Debería ver el resultado de `9`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-117">You should see the result of `9`.</span></span>

<span data-ttu-id="2a3b8-118">Navegue a la API del producto (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="2a3b8-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="2a3b8-119">Devuelve `9`, no `20` tal como se esperaría.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="2a3b8-120">Ese problema se corregirá más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="2a3b8-121">Agregar `dotnet watch` a un proyecto</span><span class="sxs-lookup"><span data-stu-id="2a3b8-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="2a3b8-122">La herramienta de monitor de archivos `dotnet watch` se incluye con la versión 2.1.300 del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="2a3b8-123">Si se usa una versión anterior del SDK de .NET Core, será necesario realizar los siguientes pasos.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="2a3b8-124">Agregue una referencia de paquete `Microsoft.DotNet.Watcher.Tools` al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="2a3b8-125">Instale el paquete `Microsoft.DotNet.Watcher.Tools` mediante la ejecución del comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="2a3b8-126">Ejecutar los comandos de la CLI de .NET Core con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2a3b8-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="2a3b8-127">Cualquier [comando de la CLI de .NET Core](/dotnet/core/tools#cli-commands) se puede ejecutar con `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="2a3b8-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-128">For example:</span></span>

| <span data-ttu-id="2a3b8-129">Comando</span><span class="sxs-lookup"><span data-stu-id="2a3b8-129">Command</span></span> | <span data-ttu-id="2a3b8-130">Comando con watch</span><span class="sxs-lookup"><span data-stu-id="2a3b8-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="2a3b8-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="2a3b8-131">dotnet run</span></span> | <span data-ttu-id="2a3b8-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="2a3b8-132">dotnet watch run</span></span> |
| <span data-ttu-id="2a3b8-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="2a3b8-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="2a3b8-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="2a3b8-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="2a3b8-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="2a3b8-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="2a3b8-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="2a3b8-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="2a3b8-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="2a3b8-137">dotnet test</span></span> | <span data-ttu-id="2a3b8-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="2a3b8-138">dotnet watch test</span></span> |

<span data-ttu-id="2a3b8-139">Ejecute `dotnet watch run` en la carpeta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="2a3b8-140">La salida de la consola indica que se ha iniciado `watch`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="2a3b8-141">La ejecución de `dotnet watch run` en una aplicación web inicia un explorador que navega a la dirección URL de la aplicación una vez lista.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="2a3b8-142">Para ello, `dotnet watch` lee la salida de la consola de la aplicación y espera el mensaje de preparado que <xref:Microsoft.AspNetCore.WebHost> muestra.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="2a3b8-143">`dotnet watch` actualiza el explorador cuando detecta cambios en los archivos inspeccionados.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="2a3b8-144">Para ello, el comando watch inserta un middleware en la aplicación que modifica las respuestas HTML creadas por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="2a3b8-145">El middleware agrega un bloque de script de JavaScript a la página que permite a `dotnet watch` indicar al explorador que actualice.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="2a3b8-146">Actualmente, los cambios en todos los archivos inspeccionados, que incluye contenido estático como los archivos *.html* y *.css*, hacen que la aplicación se vuelva a generar.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="2a3b8-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-147">`dotnet watch`:</span></span>

* <span data-ttu-id="2a3b8-148">Solo inspecciona los archivos que afectan a las compilaciones de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="2a3b8-149">Los archivos inspeccionados adicionalmente (a través de la configuración) siguen generando una compilación.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="2a3b8-150">Para obtener más información sobre la configuración, consulte [configuración de dotnet-watch](#dotnet-watch-configuration) en este documento.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="2a3b8-151">Puede usar `dotnet watch --project <PROJECT>` para especificar un proyecto para verlo.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="2a3b8-152">Por ejemplo, al ejecutar `dotnet watch --project WebApp run` desde la raíz de la aplicación de ejemplo, también se ejecutará y se verá el proyecto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="2a3b8-153">Realizar cambios con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2a3b8-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="2a3b8-154">Asegúrese de que `dotnet watch` se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="2a3b8-155">Corrija el error en el método `Product` de *MathController.cs* para que devuelva el producto y no la suma:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="2a3b8-156">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-156">Save the file.</span></span> <span data-ttu-id="2a3b8-157">La salida de la consola muestra que `dotnet watch` ha detectado un cambio de archivo y ha reiniciado la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="2a3b8-158">Compruebe que `http://localhost:<port number>/api/math/product?a=4&b=5` devuelve el resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="2a3b8-159">Ejecutar pruebas con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2a3b8-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="2a3b8-160">Vuelva a cambiar el método `Product` de *MathController.cs* para devolver la suma.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="2a3b8-161">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-161">Save the file.</span></span>
1. <span data-ttu-id="2a3b8-162">En un shell de comandos, desplácese hasta la carpeta *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="2a3b8-163">Ejecute [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="2a3b8-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="2a3b8-164">Ejecute `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="2a3b8-165">La salida que indica que se ha producido un error en una prueba y que el monitor espera cambios de archivos:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="2a3b8-166">Corrija el código del método `Product` para que devuelva el producto.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="2a3b8-167">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-167">Save the file.</span></span>

<span data-ttu-id="2a3b8-168">`dotnet watch` detecta el cambio de archivo y vuelve a ejecutar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="2a3b8-169">La salida de la consola indica que se han superado las pruebas.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="2a3b8-170">Personalizar la lista de archivos que inspeccionar</span><span class="sxs-lookup"><span data-stu-id="2a3b8-170">Customize files list to watch</span></span>

<span data-ttu-id="2a3b8-171">`dotnet-watch` realiza un seguimiento de forma predeterminada de todos los archivos que coincidan con los siguientes patrones globales:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="2a3b8-172">Se pueden agregar más elementos a la lista de control inspección editando el archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="2a3b8-173">Los elementos se pueden especificar individualmente o usando patrones globales.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="2a3b8-174">Descartar archivos de la inspección</span><span class="sxs-lookup"><span data-stu-id="2a3b8-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="2a3b8-175">`dotnet-watch` se puede configurar para pasar por alto su configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="2a3b8-176">Para omitir archivos concretos, agregue el atributo `Watch="false"` a la definición de un elemento en el archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="2a3b8-177">Proyectos de inspección personalizados</span><span class="sxs-lookup"><span data-stu-id="2a3b8-177">Custom watch projects</span></span>

<span data-ttu-id="2a3b8-178">`dotnet-watch` no queda restringido exclusivamente a proyectos de C#,</span><span class="sxs-lookup"><span data-stu-id="2a3b8-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="2a3b8-179">sino que se pueden crear proyectos de inspección personalizados para controlar distintos escenarios.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="2a3b8-180">Veamos el siguiente diseño de proyecto:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-180">Consider the following project layout:</span></span>

* <span data-ttu-id="2a3b8-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="2a3b8-181">**test/**</span></span>
  * <span data-ttu-id="2a3b8-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="2a3b8-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="2a3b8-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="2a3b8-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="2a3b8-184">Si el objetivo es inspeccionar los dos proyectos, cree un archivo de proyecto personalizado configurado para supervisar ambos proyectos:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="2a3b8-185">Para empezar a inspeccionar archivos en ambos proyectos, cambie a la carpeta *test*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="2a3b8-186">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="2a3b8-187">VSTest se ejecuta cuando un archivo de cualquiera de los proyectos de prueba cambie.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="2a3b8-188">configuración de dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="2a3b8-188">dotnet-watch configuration</span></span>

<span data-ttu-id="2a3b8-189">Algunas opciones de configuración se pueden pasar a `dotnet watch` a través de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="2a3b8-190">Las variables disponibles son:</span><span class="sxs-lookup"><span data-stu-id="2a3b8-190">The available variables are:</span></span>

| <span data-ttu-id="2a3b8-191">Parámetro</span><span class="sxs-lookup"><span data-stu-id="2a3b8-191">Setting</span></span>  | <span data-ttu-id="2a3b8-192">Descripción</span><span class="sxs-lookup"><span data-stu-id="2a3b8-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="2a3b8-193">Si se establece en "1" o "true", `dotnet watch` usa un monitor de archivo de sondeo en lugar de `FileSystemWatcher` de CoreFx.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="2a3b8-194">Se usa al inspeccionar archivos en recursos compartidos de red o volúmenes montados de Docker.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="2a3b8-195">De forma predeterminada, `dotnet watch` optimiza la compilación evitando ciertas operaciones, como ejecutar la restauración o volver a evaluar el conjunto de archivos inspeccionados en cada cambio de archivo.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="2a3b8-196">Si se establece en "1" o "true", estas optimizaciones se deshabilitan.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="2a3b8-197">`dotnet watch run` intenta iniciar los exploradores para aplicaciones web con `launchBrowser` configurado en *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="2a3b8-198">Si se establece en "1" o "true", se suprime este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="2a3b8-199">`dotnet watch run` intenta actualizar los exploradores cuando detecta cambios en los archivos.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="2a3b8-200">Si se establece en "1" o "true", se suprime este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="2a3b8-201">Este comportamiento también se suprime si se establece `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="2a3b8-202">`dotnet-watch` en GitHub</span><span class="sxs-lookup"><span data-stu-id="2a3b8-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="2a3b8-203">`dotnet-watch` forma parte del repositorio [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch) de GitHub.</span><span class="sxs-lookup"><span data-stu-id="2a3b8-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
