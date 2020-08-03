---
title: Usar varios entornos en ASP.NET Core
author: rick-anderson
description: Aprenda a controlar el comportamiento de las aplicaciones en varios entornos en aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330665"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="c88ff-103">Usar varios entornos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c88ff-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c88ff-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c88ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c88ff-105">ASP.NET Core configura el comportamiento de las aplicaciones en función del entorno en tiempo de ejecución mediante una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="c88ff-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c88ff-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="c88ff-107">Entornos</span><span class="sxs-lookup"><span data-stu-id="c88ff-107">Environments</span></span>

<span data-ttu-id="c88ff-108">Para determinar el entorno de ejecución, ASP.NET Core lee de las siguientes variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="c88ff-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="c88ff-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="c88ff-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="c88ff-110">`ASPNETCORE_ENVIRONMENT` cuando se llama a <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="c88ff-111">Las plantillas de aplicación web de ASP.NET Core predeterminadas llaman a `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="c88ff-112">El valor `ASPNETCORE_ENVIRONMENT` invalida `DOTNET_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="c88ff-113">`IHostEnvironment.EnvironmentName` se puede establecer en cualquier valor, pero el marco proporciona los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="c88ff-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="c88ff-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: El archivo [launchSettings.json](#lsj) establece `ASPNETCORE_ENVIRONMENT` en `Development` en el equipo local.</span><span class="sxs-lookup"><span data-stu-id="c88ff-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="c88ff-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Valor predeterminado si `DOTNET_ENVIRONMENT` y `ASPNETCORE_ENVIRONMENT` no se ha establecido.</span><span class="sxs-lookup"><span data-stu-id="c88ff-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="c88ff-116">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c88ff-116">The following code:</span></span>

* <span data-ttu-id="c88ff-117">Llama a [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) cuando `ASPNETCORE_ENVIRONMENT` está establecido en `Development`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="c88ff-118">Llama a [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) cuando el valor de `ASPNETCORE_ENVIRONMENT` se establece en `Staging`, `Production` o `Staging_2`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="c88ff-119">Inserta <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="c88ff-120">Este enfoque es útil cuando la aplicación solo requiere el ajuste de `Startup.Configure` para algunos entornos con diferencias de código mínimas en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="c88ff-121">Es similar al código generado por las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c88ff-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="c88ff-122">El [asistente de etiquetas de entorno](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utiliza el valor de [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) para incluir o excluir el marcado en el elemento:</span><span class="sxs-lookup"><span data-stu-id="c88ff-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="c88ff-123">La [página Acerca de](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) incluye el marcado anterior y muestra el valor de `IWebHostEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="c88ff-124">En Windows y macOS, los valores y las variables de entorno no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="c88ff-125">Los valores y las variables de entorno de Linux **distinguen mayúsculas de minúsculas** de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c88ff-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="c88ff-126">Creación de EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="c88ff-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="c88ff-127">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) utilizado en este documento se basa en un proyecto de páginas Razor denominado *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="c88ff-128">El código siguiente crea y ejecuta una aplicación web denominada *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="c88ff-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="c88ff-129">Cuando se ejecuta la aplicación, muestra algunas de las salidas siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="c88ff-130">Desarrollo y launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="c88ff-130">Development and launchSettings.json</span></span>

<span data-ttu-id="c88ff-131">El entorno de desarrollo puede habilitar características que no deben exponerse en producción.</span><span class="sxs-lookup"><span data-stu-id="c88ff-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="c88ff-132">Por ejemplo, las plantillas de ASP.NET Core habilitan la [página de excepciones para el desarrollador](xref:fundamentals/error-handling#developer-exception-page) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="c88ff-133">El entorno para el desarrollo del equipo local se puede establecer en el archivo *Properties\launchSettings.json* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="c88ff-134">Los valores de entorno establecidos en *launchSettings.json* invalidan los valores establecidos en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c88ff-135">El archivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c88ff-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="c88ff-136">Solo se usa en el equipo de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="c88ff-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="c88ff-137">No se implementa.</span><span class="sxs-lookup"><span data-stu-id="c88ff-137">Is not deployed.</span></span>
* <span data-ttu-id="c88ff-138">Contiene la configuración del perfil.</span><span class="sxs-lookup"><span data-stu-id="c88ff-138">contains profile settings.</span></span>

<span data-ttu-id="c88ff-139">El siguiente JSON muestra el archivo *launchSettings.json* para un proyecto web de ASP.NET Core denominado *EnvironmentsSample* creado con Visual Studio o `dotnet new`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="c88ff-140">El marcado anterior contiene dos perfiles:</span><span class="sxs-lookup"><span data-stu-id="c88ff-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="c88ff-141">`IIS Express`: El perfil predeterminado que se usa cuando se inicia la aplicación en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c88ff-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="c88ff-142">La clave `"commandName"` tiene el valor `"IISExpress"`, por lo tanto, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) es el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c88ff-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="c88ff-143">Puede establecer el perfil de inicio en el proyecto o en cualquier otro perfil incluido.</span><span class="sxs-lookup"><span data-stu-id="c88ff-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="c88ff-144">Por ejemplo, en la imagen siguiente, al seleccionar el nombre del proyecto se inicia el [servidor web de Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c88ff-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![Inicio de IIS Express en el menú](environments/_static/iisx2.png)
* <span data-ttu-id="c88ff-146">`EnvironmentsSample`: El nombre del perfil es el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="c88ff-147">Este perfil se utiliza de forma predeterminada al iniciar la aplicación con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="c88ff-148">La clave `"commandName"` tiene el valor `"Project"`, por lo tanto, se inicia el [servidor web de Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c88ff-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="c88ff-149">El valor de `commandName` puede especificar el servidor web que se va a iniciar.</span><span class="sxs-lookup"><span data-stu-id="c88ff-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="c88ff-150">`commandName` puede ser uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="c88ff-151">`IISExpress`: Inicia IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c88ff-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="c88ff-152">`IIS`: No se ha iniciado ningún servidor web.</span><span class="sxs-lookup"><span data-stu-id="c88ff-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="c88ff-153">Se espera que IIS esté disponible.</span><span class="sxs-lookup"><span data-stu-id="c88ff-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="c88ff-154">`Project`: Inicia Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c88ff-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="c88ff-155">La pestaña **Depurar** de las propiedades de proyecto de Visual Studio proporciona una GUI para editar el archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="c88ff-156">Los cambios realizados en los perfiles de proyecto podrían no surtir efecto hasta que se reinicie el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c88ff-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="c88ff-157">Es necesario reiniciar Kestrel para que pueda detectar los cambios realizados en su entorno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Variables de entorno de configuración de las propiedades del proyecto](environments/_static/project-properties-debug.png)

<span data-ttu-id="c88ff-159">El archivo *launchSettings.json* siguiente contiene varios perfiles:</span><span class="sxs-lookup"><span data-stu-id="c88ff-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="c88ff-160">Los perfiles se pueden seleccionar de la forma siguiente:</span><span class="sxs-lookup"><span data-stu-id="c88ff-160">Profiles can be selected:</span></span>

* <span data-ttu-id="c88ff-161">Desde la interfaz de usuario de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c88ff-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="c88ff-162">Mediante el comando [`dotnet run`](/dotnet/core/tools/dotnet-run) en un shell de comandos con la opción `--launch-profile` establecida en el nombre del perfil.</span><span class="sxs-lookup"><span data-stu-id="c88ff-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="c88ff-163">*Este enfoque solo admite perfiles de Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="c88ff-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="c88ff-164">En *launchSettings.json* no se deben almacenar secretos.</span><span class="sxs-lookup"><span data-stu-id="c88ff-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="c88ff-165">Se puede usar la [herramienta Administrador de secretos](xref:security/app-secrets) a fin de almacenar secretos para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="c88ff-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="c88ff-166">Cuando se usa [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="c88ff-167">En el ejemplo siguiente se establecen varias [variables de entorno de valores de configuración de host](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="c88ff-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="c88ff-168">El archivo *.vscode/launch.json* solo lo usa Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c88ff-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="c88ff-169">Producción</span><span class="sxs-lookup"><span data-stu-id="c88ff-169">Production</span></span>

<span data-ttu-id="c88ff-170">El entorno de producción debe configurarse para maximizar la seguridad, el [rendimiento](xref:performance/performance-best-practices) y la solidez de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="c88ff-171">Las opciones de configuración comunes que difieren de las del entorno de desarrollo son:</span><span class="sxs-lookup"><span data-stu-id="c88ff-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="c88ff-172">[Almacenamiento en caché](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="c88ff-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="c88ff-173">Los recursos del cliente se agrupan, se reducen y se atienden potencialmente desde una CDN.</span><span class="sxs-lookup"><span data-stu-id="c88ff-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="c88ff-174">Las páginas de error de diagnóstico están deshabilitadas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="c88ff-175">Las páginas de error descriptivas están habilitadas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="c88ff-176">El [registro](xref:fundamentals/logging/index) y la supervisión de la producción están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c88ff-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="c88ff-177">Por ejemplo, mediante [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="c88ff-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c88ff-178">Establecimiento del entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-178">Set the environment</span></span>

<span data-ttu-id="c88ff-179">A menudo resulta útil establecer un entorno específico para realizar las pruebas con una variable de entorno o una configuración de plataforma.</span><span class="sxs-lookup"><span data-stu-id="c88ff-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="c88ff-180">Si el entorno no está establecido, el valor predeterminado es `Production`, lo que deshabilita la mayoría de las características de depuración.</span><span class="sxs-lookup"><span data-stu-id="c88ff-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="c88ff-181">El método para establecer el entorno depende del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="c88ff-182">Cuando se compila el host, la última configuración de entorno leída por la aplicación determina el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="c88ff-183">El entorno de la aplicación no se puede cambiar mientras se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="c88ff-184">La [página Acerca de](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) muestra el valor de `IWebHostEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c88ff-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c88ff-185">Azure App Service</span></span>

<span data-ttu-id="c88ff-186">Para establecer el entorno en [Azure App Service](https://azure.microsoft.com/services/app-service/), realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="c88ff-187">Seleccione la aplicación desde la hoja **App Services**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="c88ff-188">En el grupo **Configuración**, seleccione la hoja **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="c88ff-189">En la pestaña **Configuración de aplicaciones**, seleccione **Nueva configuración de aplicación**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="c88ff-190">En la ventana **Agregar o editar la configuración de la aplicación**, escriba `ASPNETCORE_ENVIRONMENT` para el **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="c88ff-191">En **Valor**, proporcione el entorno (por ejemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="c88ff-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="c88ff-192">Active la casilla **Configuración de ranura de implementación** si quiere que la configuración del entorno permanezca con la ranura actual cuando se intercambien las ranuras de implementación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="c88ff-193">Para más información, consulte [Configuración de entornos de ensayo en Azure App Service](/azure/app-service/web-sites-staged-publishing) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="c88ff-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="c88ff-194">Seleccione **Aceptar** para cerrar la ventana **Agregar o editar la configuración de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="c88ff-195">Seleccione **Guardar** en la parte superior de la hoja **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="c88ff-196">Azure App Service reinicia automáticamente la aplicación después de que se agregue, cambie o elimine una configuración de aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c88ff-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="c88ff-197">Windows</span><span class="sxs-lookup"><span data-stu-id="c88ff-197">Windows</span></span>

<span data-ttu-id="c88ff-198">Los valores de entorno de *launchSettings.json* invalidan los valores establecidos en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c88ff-199">Para establecer `ASPNETCORE_ENVIRONMENT` en la sesión actual, cuando la aplicación se ha iniciado con [dotnet run](/dotnet/core/tools/dotnet-run), use los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="c88ff-200">**Símbolo del sistema**</span><span class="sxs-lookup"><span data-stu-id="c88ff-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="c88ff-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c88ff-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="c88ff-202">El comando anterior se establece `ASPNETCORE_ENVIRONMENT` solo para los procesos iniciados desde esa ventana de comandos.</span><span class="sxs-lookup"><span data-stu-id="c88ff-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="c88ff-203">Para establecer el valor globalmente en Windows, use cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="c88ff-204">Abra el **Panel de control** > **Sistema** > **Configuración avanzada del sistema** y agregue o edite el valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propiedades avanzadas del sistema](environments/_static/systemsetting_environment.png)

  ![Variable de entorno de ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="c88ff-207">Abra un símbolo del sistema con permisos de administrador y use el comando `setx` o abra un símbolo del sistema administrativo de PowerShell y use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="c88ff-208">**Símbolo del sistema**</span><span class="sxs-lookup"><span data-stu-id="c88ff-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="c88ff-209">El modificador `/M` indica que hay que establecer la variable de entorno en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c88ff-210">Si no se usa el modificador `/M`, la variable de entorno se establece para la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="c88ff-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="c88ff-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c88ff-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="c88ff-212">El valor de opción `Machine` indica que hay que establecer la variable de entorno en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c88ff-213">Si el valor de opción se cambia a `User`, la variable de entorno se establece para la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="c88ff-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="c88ff-214">Cuando la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece de forma global, se aplica a `dotnet run` en cualquier ventana de comandos abierta después del establecimiento del valor.</span><span class="sxs-lookup"><span data-stu-id="c88ff-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="c88ff-215">Los valores de entorno de *launchSettings.json* invalidan los valores establecidos en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c88ff-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="c88ff-216">**web.config**</span></span>

<span data-ttu-id="c88ff-217">Para establecer la variable de entorno `ASPNETCORE_ENVIRONMENT` con *web.config*, vea la sección *Establecimiento de variables de entorno* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="c88ff-218">**Archivo del proyecto o perfil de publicación**</span><span class="sxs-lookup"><span data-stu-id="c88ff-218">**Project file or publish profile**</span></span>

<span data-ttu-id="c88ff-219">**Para las implementaciones de IIS de Windows:** Incluya la propiedad `<EnvironmentName>` del [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o un archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c88ff-220">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="c88ff-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c88ff-221">**Por grupo de aplicaciones de IIS**</span><span class="sxs-lookup"><span data-stu-id="c88ff-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="c88ff-222">Para establecer la variable de entorno `ASPNETCORE_ENVIRONMENT` para una aplicación que se ejecuta en un grupo de aplicaciones aislado (se admite en IIS 10.0 o posterior), vea la sección *AppCmd.exe* del tema [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) (Variables de entorno).</span><span class="sxs-lookup"><span data-stu-id="c88ff-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="c88ff-223">Cuando la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece para un grupo de aplicaciones, su valor reemplaza a un valor en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="c88ff-224">Cuando hospede una aplicación en IIS y agregue o cambie la variable de entorno `ASPNETCORE_ENVIRONMENT`, use cualquiera de los siguientes métodos para que las aplicaciones tomen el nuevo valor:</span><span class="sxs-lookup"><span data-stu-id="c88ff-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="c88ff-225">Ejecute `net stop was /y` seguido de `net start w3svc` en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="c88ff-226">Reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="c88ff-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="c88ff-227">macOS</span><span class="sxs-lookup"><span data-stu-id="c88ff-227">macOS</span></span>

<span data-ttu-id="c88ff-228">Para establecer el entorno actual para macOS, puede hacerlo en línea al ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c88ff-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="c88ff-229">De forma alternativa, defina el entorno con `export` antes de ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c88ff-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="c88ff-230">Las variables de entorno de nivel de equipo se establecen en el archivo *.bashrc* o *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="c88ff-231">Edite el archivo con cualquier editor de texto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-231">Edit the file using any text editor.</span></span> <span data-ttu-id="c88ff-232">Agregue la siguiente instrucción:</span><span class="sxs-lookup"><span data-stu-id="c88ff-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="c88ff-233">Linux</span><span class="sxs-lookup"><span data-stu-id="c88ff-233">Linux</span></span>

<span data-ttu-id="c88ff-234">Para distribuciones de Linux, use el comando `export` en un símbolo del sistema para la configuración de variables basada en sesión y el archivo *bash_profile* para la configuración del entorno en el nivel de equipo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="c88ff-235">Establecimiento del entorno en el código</span><span class="sxs-lookup"><span data-stu-id="c88ff-235">Set the environment in code</span></span>

<span data-ttu-id="c88ff-236">Llame a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> al compilar el host.</span><span class="sxs-lookup"><span data-stu-id="c88ff-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="c88ff-237">Vea <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="c88ff-238">Configuración de entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-238">Configuration by environment</span></span>

<span data-ttu-id="c88ff-239">Para cargar la configuración por entorno, consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="c88ff-240">Métodos y clase Startup basados en entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="c88ff-241">Inserción de IWebHostEnvironment en la clase Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="c88ff-242">Inserte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el constructor de `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="c88ff-243">Este enfoque es útil cuando la aplicación solo requiere la configuración de `Startup` para algunos entornos con diferencias de código mínimas en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="c88ff-244">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c88ff-244">In the following example:</span></span>

* <span data-ttu-id="c88ff-245">El entorno se mantiene en el campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="c88ff-246">`_env` se usa en `ConfigureServices` y `Configure` para aplicar la configuración de inicio en función del entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="c88ff-247">Convenciones de la clase Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-247">Startup class conventions</span></span>

<span data-ttu-id="c88ff-248">Cuando se inicia una aplicación ASP.NET Core, la [clase Startup](xref:fundamentals/startup) arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="c88ff-249">La aplicación puede definir varias clases de `Startup` para diferentes entornos.</span><span class="sxs-lookup"><span data-stu-id="c88ff-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="c88ff-250">La clase `Startup` correspondiente se selecciona en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c88ff-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c88ff-251">La clase cuyo sufijo de nombre coincide con el entorno actual se establece como prioritaria.</span><span class="sxs-lookup"><span data-stu-id="c88ff-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c88ff-252">Si no se encuentra una clase `Startup{EnvironmentName}` coincidente, se usa la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="c88ff-253">Este enfoque es útil cuando la aplicación requiere configurar el inicio para algunos entornos con muchas diferencias de código en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="c88ff-254">Las aplicaciones típicas no necesitarán este enfoque.</span><span class="sxs-lookup"><span data-stu-id="c88ff-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="c88ff-255">Para implementar clases `Startup` basadas en entornos, cree una clase `Startup{EnvironmentName}` y una clase `Startup` de reserva:</span><span class="sxs-lookup"><span data-stu-id="c88ff-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="c88ff-256">Use la sobrecarga [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que acepta un nombre de ensamblado:</span><span class="sxs-lookup"><span data-stu-id="c88ff-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="c88ff-257">Convenciones del método Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-257">Startup method conventions</span></span>

<span data-ttu-id="c88ff-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) y [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) son compatibles con versiones específicas del entorno con el formato `Configure<EnvironmentName>` y `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="c88ff-259">Este enfoque es útil cuando la aplicación requiere configurar el inicio para algunos entornos con muchas diferencias de código en cada uno:</span><span class="sxs-lookup"><span data-stu-id="c88ff-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c88ff-260">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c88ff-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c88ff-261">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c88ff-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c88ff-262">ASP.NET Core configura el comportamiento de las aplicaciones en función del entorno en tiempo de ejecución mediante una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="c88ff-263">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c88ff-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="c88ff-264">Entornos</span><span class="sxs-lookup"><span data-stu-id="c88ff-264">Environments</span></span>

<span data-ttu-id="c88ff-265">ASP.NET Core lee la variable de entorno `ASPNETCORE_ENVIRONMENT` al inicio de la aplicación y almacena el valor en [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="c88ff-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="c88ff-266">`ASPNETCORE_ENVIRONMENT` se puede establecer en cualquier valor, pero el marco proporciona tres valores:</span><span class="sxs-lookup"><span data-stu-id="c88ff-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="c88ff-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="c88ff-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="c88ff-268">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="c88ff-268">The preceding code:</span></span>

* <span data-ttu-id="c88ff-269">Llama a [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) cuando `ASPNETCORE_ENVIRONMENT` está establecido en `Development`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="c88ff-270">Llama a [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) cuando el valor de `ASPNETCORE_ENVIRONMENT` está establecido en uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="c88ff-271">El [asistente de etiquetas de entorno](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa el valor de `IHostingEnvironment.EnvironmentName` para incluir o excluir el marcado en el elemento:</span><span class="sxs-lookup"><span data-stu-id="c88ff-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="c88ff-272">En Windows y macOS, los valores y las variables de entorno no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="c88ff-273">Los valores y las variables de entorno de Linux distinguen mayúsculas de minúsculas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c88ff-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="c88ff-274">Desarrollo</span><span class="sxs-lookup"><span data-stu-id="c88ff-274">Development</span></span>

<span data-ttu-id="c88ff-275">El entorno de desarrollo puede habilitar características que no deben exponerse en producción.</span><span class="sxs-lookup"><span data-stu-id="c88ff-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="c88ff-276">Por ejemplo, las plantillas de ASP.NET Core habilitan la [página de excepciones para el desarrollador](xref:fundamentals/error-handling#developer-exception-page) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="c88ff-277">El entorno para el desarrollo del equipo local se puede establecer en el archivo *Properties\launchSettings.json* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="c88ff-278">Los valores de entorno establecidos en *launchSettings.json* invalidan los valores establecidos en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c88ff-279">En el siguiente fragmento de JSON se muestran tres perfiles de un archivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c88ff-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="c88ff-280">La propiedad `applicationUrl` en *launchSettings.json* puede especificar una lista de direcciones URL del servidor.</span><span class="sxs-lookup"><span data-stu-id="c88ff-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="c88ff-281">Use un punto y coma entre las direcciones URL de la lista:</span><span class="sxs-lookup"><span data-stu-id="c88ff-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="c88ff-282">Cuando la aplicación se inicia con [dotnet run](/dotnet/core/tools/dotnet-run), se usa el primer perfil con `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="c88ff-283">El valor de `commandName` especifica el servidor web que se va a iniciar.</span><span class="sxs-lookup"><span data-stu-id="c88ff-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="c88ff-284">`commandName` puede ser uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="c88ff-285">`Project` (que inicia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="c88ff-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="c88ff-286">Cuando una aplicación se inicia con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="c88ff-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="c88ff-287">Se lee *launchSettings.json*, si está disponible.</span><span class="sxs-lookup"><span data-stu-id="c88ff-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="c88ff-288">La configuración de `environmentVariables` de *launchSettings.json* reemplaza las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="c88ff-289">Se muestra el entorno de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="c88ff-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="c88ff-290">En la siguiente salida se muestra una aplicación que se ha iniciado con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="c88ff-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="c88ff-291">La pestaña **Depurar** de las propiedades de proyecto de Visual Studio proporciona una GUI para editar el archivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c88ff-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Variables de entorno de configuración de las propiedades del proyecto](environments/_static/project-properties-debug.png)

<span data-ttu-id="c88ff-293">Los cambios realizados en los perfiles de proyecto podrían no surtir efecto hasta que se reinicie el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c88ff-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="c88ff-294">Es necesario reiniciar Kestrel para que pueda detectar los cambios realizados en su entorno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="c88ff-295">En *launchSettings.json* no se deben almacenar secretos.</span><span class="sxs-lookup"><span data-stu-id="c88ff-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="c88ff-296">Se puede usar la [herramienta Administrador de secretos](xref:security/app-secrets) a fin de almacenar secretos para el desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="c88ff-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="c88ff-297">Cuando se usa [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="c88ff-298">En el siguiente ejemplo se establece el entorno en `Development`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="c88ff-299">Un archivo *.vscode/launch.json* del proyecto no se lee al iniciar la aplicación con `dotnet run` en la misma manera que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="c88ff-300">Cuando se inicia una aplicación en desarrollo que no tiene un archivo *launchSettings.json*, establezca el valor del entorno con una variable de entorno o con un argumento de línea de comandos para el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="c88ff-301">Producción</span><span class="sxs-lookup"><span data-stu-id="c88ff-301">Production</span></span>

<span data-ttu-id="c88ff-302">El entorno de producción debe configurarse para maximizar la seguridad, el rendimiento y la solidez de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="c88ff-303">Las opciones de configuración comunes que difieren de las del entorno de desarrollo son:</span><span class="sxs-lookup"><span data-stu-id="c88ff-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="c88ff-304">Almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="c88ff-304">Caching.</span></span>
* <span data-ttu-id="c88ff-305">Los recursos del cliente se agrupan, se reducen y se atienden potencialmente desde una CDN.</span><span class="sxs-lookup"><span data-stu-id="c88ff-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="c88ff-306">Las páginas de error de diagnóstico están deshabilitadas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="c88ff-307">Las páginas de error descriptivas están habilitadas.</span><span class="sxs-lookup"><span data-stu-id="c88ff-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="c88ff-308">El registro y la supervisión de la producción están habilitados.</span><span class="sxs-lookup"><span data-stu-id="c88ff-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="c88ff-309">Por ejemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="c88ff-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c88ff-310">Establecimiento del entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-310">Set the environment</span></span>

<span data-ttu-id="c88ff-311">A menudo resulta útil establecer un entorno específico para realizar las pruebas con una variable de entorno o una configuración de plataforma.</span><span class="sxs-lookup"><span data-stu-id="c88ff-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="c88ff-312">Si el entorno no está establecido, el valor predeterminado es `Production`, lo que deshabilita la mayoría de las características de depuración.</span><span class="sxs-lookup"><span data-stu-id="c88ff-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="c88ff-313">El método para establecer el entorno depende del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="c88ff-314">Cuando se compila el host, la última configuración de entorno leída por la aplicación determina el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="c88ff-315">El entorno de la aplicación no se puede cambiar mientras se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="c88ff-316">Configuración de plataforma o variable de entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="c88ff-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c88ff-317">Azure App Service</span></span>

<span data-ttu-id="c88ff-318">Para establecer el entorno en [Azure App Service](https://azure.microsoft.com/services/app-service/), realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="c88ff-319">Seleccione la aplicación desde la hoja **App Services**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="c88ff-320">En el grupo **Configuración**, seleccione la hoja **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="c88ff-321">En la pestaña **Configuración de aplicaciones**, seleccione **Nueva configuración de aplicación**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="c88ff-322">En la ventana **Agregar o editar la configuración de la aplicación**, escriba `ASPNETCORE_ENVIRONMENT` para el **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="c88ff-323">En **Valor**, proporcione el entorno (por ejemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="c88ff-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="c88ff-324">Active la casilla **Configuración de ranura de implementación** si quiere que la configuración del entorno permanezca con la ranura actual cuando se intercambien las ranuras de implementación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="c88ff-325">Para más información, consulte [Configuración de entornos de ensayo en Azure App Service](/azure/app-service/web-sites-staged-publishing) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="c88ff-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="c88ff-326">Seleccione **Aceptar** para cerrar la ventana **Agregar o editar la configuración de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="c88ff-327">Seleccione **Guardar** en la parte superior de la hoja **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="c88ff-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="c88ff-328">Azure App Service reinicia automáticamente la aplicación después de que se agregue, cambie o elimine una configuración de aplicación (variable de entorno) en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c88ff-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="c88ff-329">Windows</span><span class="sxs-lookup"><span data-stu-id="c88ff-329">Windows</span></span>

<span data-ttu-id="c88ff-330">Para establecer `ASPNETCORE_ENVIRONMENT` en la sesión actual, cuando la aplicación se ha iniciado con [dotnet run](/dotnet/core/tools/dotnet-run), use los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="c88ff-331">**Símbolo del sistema**</span><span class="sxs-lookup"><span data-stu-id="c88ff-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="c88ff-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c88ff-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="c88ff-333">Estos comandos solo tienen efecto en la ventana actual.</span><span class="sxs-lookup"><span data-stu-id="c88ff-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="c88ff-334">Cuando se cierre la ventana, la configuración de `ASPNETCORE_ENVIRONMENT` volverá a la configuración predeterminada o al valor del equipo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="c88ff-335">Para establecer el valor globalmente en Windows, use cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="c88ff-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="c88ff-336">Abra el **Panel de control** > **Sistema** > **Configuración avanzada del sistema** y agregue o edite el valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propiedades avanzadas del sistema](environments/_static/systemsetting_environment.png)

  ![Variable de entorno de ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="c88ff-339">Abra un símbolo del sistema con permisos de administrador y use el comando `setx` o abra un símbolo del sistema administrativo de PowerShell y use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="c88ff-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="c88ff-340">**Símbolo del sistema**</span><span class="sxs-lookup"><span data-stu-id="c88ff-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="c88ff-341">El modificador `/M` indica que hay que establecer la variable de entorno en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c88ff-342">Si no se usa el modificador `/M`, la variable de entorno se establece para la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="c88ff-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="c88ff-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c88ff-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="c88ff-344">El valor de opción `Machine` indica que hay que establecer la variable de entorno en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c88ff-345">Si el valor de opción se cambia a `User`, la variable de entorno se establece para la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="c88ff-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="c88ff-346">Cuando la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece de forma global, se aplica a `dotnet run` en cualquier ventana de comandos abierta después del establecimiento del valor.</span><span class="sxs-lookup"><span data-stu-id="c88ff-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="c88ff-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="c88ff-347">**web.config**</span></span>

<span data-ttu-id="c88ff-348">Para establecer la variable de entorno `ASPNETCORE_ENVIRONMENT` con *web.config*, vea la sección *Establecimiento de variables de entorno* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="c88ff-349">**Archivo del proyecto o perfil de publicación**</span><span class="sxs-lookup"><span data-stu-id="c88ff-349">**Project file or publish profile**</span></span>

<span data-ttu-id="c88ff-350">**Para las implementaciones de IIS de Windows:** Incluya la propiedad `<EnvironmentName>` del [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o un archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c88ff-351">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="c88ff-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c88ff-352">**Por grupo de aplicaciones de IIS**</span><span class="sxs-lookup"><span data-stu-id="c88ff-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="c88ff-353">Para establecer la variable de entorno `ASPNETCORE_ENVIRONMENT` para una aplicación que se ejecuta en un grupo de aplicaciones aislado (se admite en IIS 10.0 o posterior), vea la sección *AppCmd.exe* del tema [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) (Variables de entorno).</span><span class="sxs-lookup"><span data-stu-id="c88ff-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="c88ff-354">Cuando la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece para un grupo de aplicaciones, su valor reemplaza a un valor en el nivel de sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c88ff-355">Cuando hospede una aplicación en IIS y agregue o cambie la variable de entorno `ASPNETCORE_ENVIRONMENT`, use cualquiera de los siguientes métodos para que las aplicaciones tomen el nuevo valor:</span><span class="sxs-lookup"><span data-stu-id="c88ff-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="c88ff-356">Ejecute `net stop was /y` seguido de `net start w3svc` en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="c88ff-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="c88ff-357">Reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="c88ff-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="c88ff-358">macOS</span><span class="sxs-lookup"><span data-stu-id="c88ff-358">macOS</span></span>

<span data-ttu-id="c88ff-359">Para establecer el entorno actual para macOS, puede hacerlo en línea al ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c88ff-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="c88ff-360">De forma alternativa, defina el entorno con `export` antes de ejecutar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c88ff-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="c88ff-361">Las variables de entorno de nivel de equipo se establecen en el archivo *.bashrc* o *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="c88ff-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="c88ff-362">Edite el archivo con cualquier editor de texto.</span><span class="sxs-lookup"><span data-stu-id="c88ff-362">Edit the file using any text editor.</span></span> <span data-ttu-id="c88ff-363">Agregue la siguiente instrucción:</span><span class="sxs-lookup"><span data-stu-id="c88ff-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="c88ff-364">Linux</span><span class="sxs-lookup"><span data-stu-id="c88ff-364">Linux</span></span>

<span data-ttu-id="c88ff-365">Para distribuciones de Linux, use el comando `export` en un símbolo del sistema para la configuración de variables basada en sesión y el archivo *bash_profile* para la configuración del entorno en el nivel de equipo.</span><span class="sxs-lookup"><span data-stu-id="c88ff-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="c88ff-366">Establecimiento del entorno en el código</span><span class="sxs-lookup"><span data-stu-id="c88ff-366">Set the environment in code</span></span>

<span data-ttu-id="c88ff-367">Llame a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> al compilar el host.</span><span class="sxs-lookup"><span data-stu-id="c88ff-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="c88ff-368">Vea <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="c88ff-369">Configuración de entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-369">Configuration by environment</span></span>

<span data-ttu-id="c88ff-370">Para cargar la configuración por entorno, se recomienda lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c88ff-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="c88ff-371">Archivos *appsettings* (*appsettings.{Entorno}.json*).</span><span class="sxs-lookup"><span data-stu-id="c88ff-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="c88ff-372">Vea <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="c88ff-373">Variables de entorno (establecidas en todos los sistemas donde se hospede la aplicación).</span><span class="sxs-lookup"><span data-stu-id="c88ff-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="c88ff-374">Vea <xref:fundamentals/host/web-host#environment> y <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="c88ff-375">Administrador de secretos (solo en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="c88ff-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="c88ff-376">Vea <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="c88ff-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="c88ff-377">Métodos y clase Startup basados en entorno</span><span class="sxs-lookup"><span data-stu-id="c88ff-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="c88ff-378">Inserción de IHostingEnvironment en Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="c88ff-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="c88ff-379">Inserte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="c88ff-380">Este enfoque es útil cuando la aplicación solo requiere la configuración de `Startup.Configure` para algunos entornos con diferencias de código mínimas en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="c88ff-381">Inserción de IHostingEnvironment en la clase Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="c88ff-382">Inserte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> en el constructor `Startup` y asigne el servicio a un campo para su uso en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="c88ff-383">Este enfoque es útil cuando la aplicación solo requiere configurar el inicio para algunos entornos con diferencias de código mínimas en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="c88ff-384">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c88ff-384">In the following example:</span></span>

* <span data-ttu-id="c88ff-385">El entorno se mantiene en el campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="c88ff-386">`_env` se usa en `ConfigureServices` y `Configure` para aplicar la configuración de inicio en función del entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="c88ff-387">Convenciones de la clase Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-387">Startup class conventions</span></span>

<span data-ttu-id="c88ff-388">Cuando se inicia una aplicación ASP.NET Core, la [clase Startup](xref:fundamentals/startup) arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c88ff-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="c88ff-389">La aplicación puede definir clases `Startup` independientes para distintos entornos (por ejemplo, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="c88ff-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="c88ff-390">La clase `Startup` correspondiente se selecciona en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c88ff-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c88ff-391">La clase cuyo sufijo de nombre coincide con el entorno actual se establece como prioritaria.</span><span class="sxs-lookup"><span data-stu-id="c88ff-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c88ff-392">Si no se encuentra una clase `Startup{EnvironmentName}` coincidente, se usa la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="c88ff-393">Este enfoque es útil cuando la aplicación requiere configurar el inicio para algunos entornos con muchas diferencias de código en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="c88ff-394">Para implementar clases `Startup` basadas en entornos, cree una clase `Startup{EnvironmentName}` para cada entorno en uso y una clase `Startup` de reserva:</span><span class="sxs-lookup"><span data-stu-id="c88ff-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="c88ff-395">Use la sobrecarga [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que acepta un nombre de ensamblado:</span><span class="sxs-lookup"><span data-stu-id="c88ff-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="c88ff-396">Convenciones del método Startup</span><span class="sxs-lookup"><span data-stu-id="c88ff-396">Startup method conventions</span></span>

<span data-ttu-id="c88ff-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) y [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) son compatibles con versiones específicas del entorno con el formato `Configure<EnvironmentName>` y `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="c88ff-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="c88ff-398">Este enfoque es útil cuando la aplicación requiere configurar el inicio para algunos entornos con muchas diferencias de código en cada uno.</span><span class="sxs-lookup"><span data-stu-id="c88ff-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="c88ff-399">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c88ff-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
