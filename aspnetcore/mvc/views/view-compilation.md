---
title: Razorcompilación de archivos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo Razor se produce la compilación de archivos en una aplicación ASP.net Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: fc7924f8f8b321ae017b7acd729fe11c4e0e3c7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021086"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="af5f6-103">Razorcompilación de archivos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af5f6-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="af5f6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af5f6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="af5f6-105">Razorlos archivos con una extensión *. cshtml* se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="af5f6-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="af5f6-106">La compilación en tiempo de ejecución puede habilitarse opcionalmente mediante la configuración del proyecto.</span><span class="sxs-lookup"><span data-stu-id="af5f6-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="af5f6-107">Razorprevia</span><span class="sxs-lookup"><span data-stu-id="af5f6-107">Razor compilation</span></span>

<span data-ttu-id="af5f6-108">RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y en tiempo de publicación Razor .</span><span class="sxs-lookup"><span data-stu-id="af5f6-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="af5f6-109">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite Razor actualizar los archivos si se modifican.</span><span class="sxs-lookup"><span data-stu-id="af5f6-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="af5f6-110">Habilitar compilación en tiempo de ejecución al crear el proyecto</span><span class="sxs-lookup"><span data-stu-id="af5f6-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="af5f6-111">Las Razor plantillas de proyecto pages y MVC incluyen una opción para habilitar la compilación en tiempo de ejecución cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af5f6-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="af5f6-112">Esta opción se admite en ASP.NET Core 3,1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="af5f6-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af5f6-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af5f6-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="af5f6-114">En el cuadro de diálogo **crear una nueva aplicación Web de ASP.net Core** :</span><span class="sxs-lookup"><span data-stu-id="af5f6-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="af5f6-115">Seleccione la plantilla de **proyecto aplicación web o** **aplicación web (controlador de vista de modelos)** .</span><span class="sxs-lookup"><span data-stu-id="af5f6-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="af5f6-116">Active la casilla **habilitar Razor compilación en tiempo de ejecución** .</span><span class="sxs-lookup"><span data-stu-id="af5f6-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af5f6-117">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="af5f6-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="af5f6-118">Use la `-rrc` `--razor-runtime-compilation` opción de plantilla o.</span><span class="sxs-lookup"><span data-stu-id="af5f6-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="af5f6-119">Por ejemplo, el siguiente comando crea un nuevo Razor proyecto de páginas con la compilación en tiempo de ejecución habilitada:</span><span class="sxs-lookup"><span data-stu-id="af5f6-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="af5f6-120">Habilitar la compilación en tiempo de ejecución en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="af5f6-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="af5f6-121">Para habilitar la compilación en tiempo de ejecución para todos los entornos de un proyecto existente:</span><span class="sxs-lookup"><span data-stu-id="af5f6-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="af5f6-122">Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Paquete NuGet de RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="af5f6-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="af5f6-123">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="af5f6-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="af5f6-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="af5f6-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="af5f6-125">Habilitar la compilación en tiempo de ejecución de forma condicional en un proyecto existente</span><span class="sxs-lookup"><span data-stu-id="af5f6-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="af5f6-126">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="af5f6-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="af5f6-127">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="af5f6-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="af5f6-128">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="af5f6-128">Uses compiled views.</span></span>
* <span data-ttu-id="af5f6-129">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="af5f6-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="af5f6-130">Para habilitar la compilación en tiempo de ejecución solo en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="af5f6-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="af5f6-131">Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Paquete NuGet de RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="af5f6-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="af5f6-132">Modifique la sección de perfil `environmentVariables` de inicio de *launchSettings.jsen*:</span><span class="sxs-lookup"><span data-stu-id="af5f6-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="af5f6-133">Compruebe `ASPNETCORE_ENVIRONMENT` que está establecido en `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="af5f6-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="af5f6-134">Establezca `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` en `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="af5f6-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="af5f6-135">En el ejemplo siguiente, la compilación en tiempo de ejecución está habilitada en el entorno de desarrollo para los `IIS Express` `RazorPagesApp` perfiles de inicio y:</span><span class="sxs-lookup"><span data-stu-id="af5f6-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="af5f6-136">No se necesitan cambios en el código en la `Startup` clase del proyecto.</span><span class="sxs-lookup"><span data-stu-id="af5f6-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="af5f6-137">En tiempo de ejecución, ASP.NET Core busca un [atributo HostingStartup de nivel de ensamblado](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) en `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="af5f6-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="af5f6-138">El `HostingStartup` atributo especifica el código de inicio de la aplicación que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="af5f6-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="af5f6-139">Ese código de inicio habilita la compilación en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="af5f6-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="af5f6-140">Habilitar la compilación en tiempo de ejecución para una Razor biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="af5f6-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="af5f6-141">Considere un escenario en el que un Razor proyecto de páginas hace referencia a una [ Razor biblioteca de clases (RCL)](xref:razor-pages/ui-class) denominada *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="af5f6-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="af5f6-142">RCL contiene un archivo *_Layout. cshtml* que consumen todos los proyectos de MVC y Pages de su equipo Razor .</span><span class="sxs-lookup"><span data-stu-id="af5f6-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="af5f6-143">Quiere habilitar la compilación en tiempo de ejecución para el archivo *_Layout. cshtml* en esa RCL.</span><span class="sxs-lookup"><span data-stu-id="af5f6-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="af5f6-144">Realice los cambios siguientes en el Razor proyecto de páginas:</span><span class="sxs-lookup"><span data-stu-id="af5f6-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="af5f6-145">Habilite la compilación en tiempo de ejecución con las instrucciones [para habilitar de forma condicional la compilación en tiempo de ejecución en un proyecto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="af5f6-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="af5f6-146">Configurar las opciones de compilación en tiempo de ejecución en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af5f6-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="af5f6-147">En el código anterior, se construye una ruta de acceso absoluta al RCL de *MyClassLib* .</span><span class="sxs-lookup"><span data-stu-id="af5f6-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="af5f6-148">La [API de PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) se usa para buscar directorios y archivos en esa ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="af5f6-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="af5f6-149">Por último, la `PhysicalFileProvider` instancia se agrega a una colección de proveedores de archivos, que permite el acceso a los archivos *. CSHTML* de RCL.</span><span class="sxs-lookup"><span data-stu-id="af5f6-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af5f6-150">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af5f6-150">Additional resources</span></span>

* <span data-ttu-id="af5f6-151">Propiedades [ Razor CompileOnBuild y Razor CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="af5f6-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="af5f6-152">Razorlos archivos con una extensión *. cshtml* se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="af5f6-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="af5f6-153">La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="af5f6-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="af5f6-154">Razorprevia</span><span class="sxs-lookup"><span data-stu-id="af5f6-154">Razor compilation</span></span>

<span data-ttu-id="af5f6-155">RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y en tiempo de publicación Razor .</span><span class="sxs-lookup"><span data-stu-id="af5f6-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="af5f6-156">Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite Razor actualizar los archivos si se modifican.</span><span class="sxs-lookup"><span data-stu-id="af5f6-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="af5f6-157">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="af5f6-157">Runtime compilation</span></span>

<span data-ttu-id="af5f6-158">Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:</span><span class="sxs-lookup"><span data-stu-id="af5f6-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="af5f6-159">Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Paquete NuGet de RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="af5f6-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="af5f6-160">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="af5f6-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="af5f6-161">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="af5f6-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="af5f6-162">Habilitación condicional de la compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="af5f6-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="af5f6-163">La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="af5f6-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="af5f6-164">Este modo de habilitación condicional garantiza que la salida publicada:</span><span class="sxs-lookup"><span data-stu-id="af5f6-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="af5f6-165">Usa vistas precompiladas.</span><span class="sxs-lookup"><span data-stu-id="af5f6-165">Uses compiled views.</span></span>
* <span data-ttu-id="af5f6-166">Tiene un tamaño inferior.</span><span class="sxs-lookup"><span data-stu-id="af5f6-166">Is smaller in size.</span></span>
* <span data-ttu-id="af5f6-167">No habilita monitores de archivos en producción.</span><span class="sxs-lookup"><span data-stu-id="af5f6-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="af5f6-168">Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:</span><span class="sxs-lookup"><span data-stu-id="af5f6-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="af5f6-169">Haga referencia condicionalmente a [Microsoft. AspNetCore. Mvc. Razor . Paquete RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el `Configuration` valor activo:</span><span class="sxs-lookup"><span data-stu-id="af5f6-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="af5f6-170">Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="af5f6-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="af5f6-171">Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:</span><span class="sxs-lookup"><span data-stu-id="af5f6-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="af5f6-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af5f6-172">Additional resources</span></span>

* <span data-ttu-id="af5f6-173">Propiedades [ Razor CompileOnBuild y Razor CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="af5f6-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="af5f6-174">Vea el [ejemplo de compilación en tiempo de ejecución en github](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo se realiza el trabajo de compilación en tiempo de ejecución entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="af5f6-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af5f6-175">Un Razor archivo se compila en tiempo de ejecución, cuando Razor se invoca la vista de MVC o la página asociada.</span><span class="sxs-lookup"><span data-stu-id="af5f6-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="af5f6-176">Razorlos archivos se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="af5f6-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="af5f6-177">Razorprevia</span><span class="sxs-lookup"><span data-stu-id="af5f6-177">Razor compilation</span></span>

<span data-ttu-id="af5f6-178">RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y de publicación Razor .</span><span class="sxs-lookup"><span data-stu-id="af5f6-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="af5f6-179">La edición Razor de archivos tras su actualización se admite en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="af5f6-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="af5f6-180">De forma predeterminada, solo se implementan en la aplicación los archivos *Views.dll* compilados y los archivos *. cshtml* o los ensamblados de referencia necesarios para compilar Razor archivos.</span><span class="sxs-lookup"><span data-stu-id="af5f6-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af5f6-181">La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="af5f6-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="af5f6-182">Se recomienda migrar al [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="af5f6-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="af5f6-183">El Razor SDK solo es efectivo cuando no hay ninguna propiedad específica de precompilación establecida en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="af5f6-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="af5f6-184">Por ejemplo, al establecer la propiedad del archivo *. csproj* `MvcRazorCompileOnPublish` en, se `true` deshabilita el Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="af5f6-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="af5f6-185">Compilación en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="af5f6-185">Runtime compilation</span></span>

<span data-ttu-id="af5f6-186">La compilación en tiempo de compilación se complementa por la compilación en tiempo de ejecución de Razor los archivos.</span><span class="sxs-lookup"><span data-stu-id="af5f6-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="af5f6-187">ASP.NET Core MVC volverá a compilar Razor los archivos cuando cambie el contenido de un archivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="af5f6-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af5f6-188">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af5f6-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
