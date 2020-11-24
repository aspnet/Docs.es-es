---
title: Configuración en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la API de configuración para una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c04dcc65f7518d2d8b32cdce7a7fbb756dd8ec3a
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417544"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="ae5ba-103">Configuración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae5ba-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="ae5ba-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae5ba-105">La configuración de ASP.NET Core se realiza mediante uno o varios [proveedores de configuración](#cp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="ae5ba-106">Los proveedores de configuración leen los datos sobre los ajustes de los pares clave-valor mediante distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="ae5ba-107">Archivos de configuración, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="ae5ba-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="ae5ba-108">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-108">Environment variables</span></span>
* <span data-ttu-id="ae5ba-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-109">Azure Key Vault</span></span>
* <span data-ttu-id="ae5ba-110">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="ae5ba-110">Azure App Configuration</span></span>
* <span data-ttu-id="ae5ba-111">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-111">Command-line arguments</span></span>
* <span data-ttu-id="ae5ba-112">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="ae5ba-113">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="ae5ba-113">Directory files</span></span>
* <span data-ttu-id="ae5ba-114">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-114">In-memory .NET objects</span></span>

<span data-ttu-id="ae5ba-115">En este tema se proporciona información sobre la configuración de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="ae5ba-116">Para más información sobre el uso de la configuración en las aplicaciones de consola, consulte [Configuración de .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="ae5ba-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ae5ba-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="ae5ba-118">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-118">Default configuration</span></span>

<span data-ttu-id="ae5ba-119">Las aplicaciones web de ASP.NET Core creadas con [dotnet new](/dotnet/core/tools/dotnet-new) o con Visual Studio generan el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="ae5ba-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="ae5ba-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  agrega un elemento `IConfiguration` existente como origen.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="ae5ba-122">En el caso de una configuración predeterminada, agrega la configuración del [host](#hvac) y lo establece como el primer origen para la configuración de la _aplicación_.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="ae5ba-123">[appsettings.json](#appsettingsjson) con el proveedor de configuración [JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="ae5ba-124">*appsettings.* `Environment` *.json* con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="ae5ba-125">Por ejemplo, *appsettings*.***Production\*\*_._json* y *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="ae5ba-126">[Secretos de la aplicación](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="ae5ba-127">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="ae5ba-128">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="ae5ba-129">Los proveedores de configuración que se agregan posteriormente invalidan los ajustes de configuración de la clave anteriores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="ae5ba-130">Por ejemplo, si `MyKey` se establece tanto en *appsettings.json* como en el entorno, se usará el valor del entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="ae5ba-131">Con los proveedores de configuración predeterminados, el [proveedor de configuración de línea de comandos](#clcp) reemplaza al resto de proveedores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="ae5ba-132">Para obtener más información sobre `CreateDefaultBuilder`, consulte el artículo [Configuración predeterminada del generador](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="ae5ba-133">El código siguiente muestra los proveedores de configuración habilitados en el orden en el que se han agregado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="ae5ba-134">Fíjese en el siguiente archivo *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="ae5ba-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="ae5ba-135">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-136">El elemento <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> predeterminado carga la configuración en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="ae5ba-137">*appsettings.* `Environment` *.json*: Por ejemplo, los archivos *appsettings*.***Production\*\*_._json* y *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="ae5ba-138">La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="ae5ba-139">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ae5ba-140">Los valores de *appsettings*.`Environment`.*json* invalidan las claves de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="ae5ba-141">Por ejemplo, de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-141">For example, by default:</span></span>

* <span data-ttu-id="ae5ba-142">En el desarrollo, la configuración de *appsettings*.***Development** _._json* sobrescribe los valores que se encuentran en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="ae5ba-143">En la producción, la configuración de *appsettings*.***Production** _._json* sobrescribe los valores que se encuentran en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="ae5ba-144">Por ejemplo, al implementar la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="ae5ba-145">Enlace de datos de configuración jerárquica mediante el patrón de opciones</span><span class="sxs-lookup"><span data-stu-id="ae5ba-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="ae5ba-146">Si usa la configuración [predeterminada](#default), los archivos *appsettings.json* and *appsettings.* `Environment` *.json* están habilitados con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="ae5ba-147">Los cambios realizados en los archivos *appsettings.json* y *appsettings.* `Environment` *.json* \***después** _ de iniciar la aplicación los lee el [proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="ae5ba-148">Consulte la sección [Proveedor de configuración JSON](#jcp) en este artículo para obtener información sobre cómo agregar archivos de configuración JSON adicionales.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="ae5ba-149">Combinación de colecciones de servicios</span><span class="sxs-lookup"><span data-stu-id="ae5ba-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="ae5ba-150">Administrador de seguridad y secretos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-150">Security and secret manager</span></span>

<span data-ttu-id="ae5ba-151">Directrices para los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-151">Configuration data guidelines:</span></span>

<span data-ttu-id="ae5ba-152">_Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="ae5ba-153">Se puede usar el [administrador de secretos](xref:security/app-secrets) para almacenar secretos en entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="ae5ba-154">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="ae5ba-155">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="ae5ba-156">De forma [predeterminada](#default), el [Administrador de secretos](xref:security/app-secrets) lee los ajustes de configuración después de los archivos *appsettings.json* y *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="ae5ba-157">Para obtener más información sobre cómo almacenar contraseñas u otros datos confidenciales consulte:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="ae5ba-158"><xref:security/app-secrets>:  incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="ae5ba-159">El administrador de secretos usa el [proveedor de configuración de archivo](#fcp) para almacenar secretos de usuario en un archivo JSON en el sistema local.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="ae5ba-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="ae5ba-161">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="ae5ba-162">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-162">Environment variables</span></span>

<span data-ttu-id="ae5ba-163">Al usar la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de los pares clave-valor de la variable de entorno después de leer *appsettings.json* , *appsettings.* `Environment` *.json* y el [Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="ae5ba-164">Por lo tanto, los valores de clave que se leen del entorno reemplazan los valores que se leen de *appsettings.json* , *appsettings.* `Environment` *.json* y del Administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ae5ba-165">Los siguientes comandos `set`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-165">The following `set` commands:</span></span>

* <span data-ttu-id="ae5ba-166">Establecen las claves de entorno y los valores del [ejemplo anterior](#appsettingsjson) en Windows.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="ae5ba-167">Prueban la configuración al usar la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="ae5ba-168">El comando `dotnet run` debe ejecutarse en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="ae5ba-169">Los ajustes de configuración del entorno anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-169">The preceding environment settings:</span></span>

* <span data-ttu-id="ae5ba-170">Solo se establecen en procesos iniciados desde la ventana de comandos en la que se establecieron.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="ae5ba-171">No los podrán leer los exploradores que se inician con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="ae5ba-172">Los siguientes comandos [setx](/windows-server/administration/windows-commands/setx) se pueden usar para establecer las claves de entorno y los valores en Windows.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="ae5ba-173">A diferencia de `set`, la configuración de `setx` se conserva.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="ae5ba-174">`/M` establece la variable en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="ae5ba-175">Si no se usa el modificador `/M`, se establece una variable de entorno de usuario.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="ae5ba-176">Para comprobar que los comandos anteriores invalidan *appsettings.json* y *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="ae5ba-177">Con Visual Studio: salga y reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="ae5ba-178">Con la CLI: abra una nueva ventana de comandos y escriba `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="ae5ba-179">Llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una cadena para especificar un prefijo para las variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="ae5ba-180">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-180">In the preceding code:</span></span>

* <span data-ttu-id="ae5ba-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="ae5ba-182">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="ae5ba-183">Las variables de entorno establecidas con el prefijo `MyCustomPrefix_` invalidan los proveedores de configuración [predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="ae5ba-184">Esto incluye las variables de entorno sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="ae5ba-185">El prefijo se quita cuando se leen los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="ae5ba-186">Los siguientes comandos prueban el prefijo personalizado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="ae5ba-187">La [configuración predeterminada](#default) carga las variables de entorno y los argumentos de la línea de comandos con los prefijos `DOTNET_` y `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="ae5ba-188">ASP.NET Core usa los prefijos `DOTNET_` y `ASPNETCORE_` para la [configuración del host y la aplicación](xref:fundamentals/host/generic-host#host-configuration), pero no para la del usuario.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="ae5ba-189">Para obtener más información sobre la configuración del host y de la aplicación, consulte el artículo [Host genérico de .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="ae5ba-190">En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="ae5ba-191">Los ajustes de configuración de Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="ae5ba-192">Se cifran en reposo y se transmiten a través de un canal cifrado.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="ae5ba-193">Se exponen como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-193">Exposed as environment variables.</span></span>

<span data-ttu-id="ae5ba-194">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="ae5ba-195">Consulte la sección [Prefijos de cadena de conexión](#constr) para obtener información sobre las cadenas de conexión de base de datos de Azure.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="ae5ba-196">Nomenclatura de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-196">Naming of environment variables</span></span>

<span data-ttu-id="ae5ba-197">Los nombres de las variables de entorno reflejan la estructura de un archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-197">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="ae5ba-198">Cada elemento de la jerarquía está separado por un carácter de subrayado doble (preferible) o un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-198">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="ae5ba-199">Cuando la estructura del elemento incluye una matriz, el índice de la matriz se debe tratar como un nombre de elemento adicional en esta ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-199">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="ae5ba-200">Considere el siguiente archivo *appsettings.json* y sus valores equivalentes representados como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-200">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="ae5ba-201">**variables de entorno**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-201">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="ae5ba-202">Variables de entorno configuradas en launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="ae5ba-202">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="ae5ba-203">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-203">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="ae5ba-204">Línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-204">Command-line</span></span>

<span data-ttu-id="ae5ba-205">Si se usa la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de los pares de clave-valor de argumento de la línea de comandos después de los siguientes orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-205">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="ae5ba-206">Los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-206">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="ae5ba-207">[Secretos de aplicación (administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-207">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ae5ba-208">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-208">Environment variables.</span></span>

<span data-ttu-id="ae5ba-209">De [forma predeterminada](#default), los valores de configuración establecidos en la línea de comandos reemplazan los valores de configuración establecidos con el resto de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-209">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="ae5ba-210">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-210">Command-line arguments</span></span>

<span data-ttu-id="ae5ba-211">El comando siguiente establece las claves y los valores mediante `=`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-211">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="ae5ba-212">El comando siguiente establece las claves y los valores mediante `/`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-212">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="ae5ba-213">El comando siguiente establece las claves y los valores mediante `--`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-213">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="ae5ba-214">El valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-214">The key value:</span></span>

* <span data-ttu-id="ae5ba-215">Debe seguir a un signo `=`, o bien la clave debe tener un prefijo `--` o `/` cuando el valor sigue a un espacio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-215">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="ae5ba-216">No es necesario si se usa `=`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-216">Isn't required if `=` is used.</span></span> <span data-ttu-id="ae5ba-217">Por ejemplo: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-217">For example, `MySetting=`.</span></span>

<span data-ttu-id="ae5ba-218">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan `=` con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-218">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="ae5ba-219">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="ae5ba-219">Switch mappings</span></span>

<span data-ttu-id="ae5ba-220">Las asignaciones de modificador admiten la lógica de sustitución de nombres de **clave**.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-220">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="ae5ba-221">Proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-221">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="ae5ba-222">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-222">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="ae5ba-223">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-223">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="ae5ba-224">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-224">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="ae5ba-225">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-225">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="ae5ba-226">Los modificadores deben comenzar con `-` o `--`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-226">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="ae5ba-227">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-227">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="ae5ba-228">Para usar un diccionario de asignaciones de modificador, páselo a la llamada a `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-228">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="ae5ba-229">El código siguiente muestra los valores de clave para las claves reemplazadas:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-229">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-230">El siguiente comando sirve para probar el reemplazo de claves:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-230">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="ae5ba-231">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-231">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="ae5ba-232">En la llamada de `AddCommandLine` al método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-232">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae5ba-233">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-233">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="ae5ba-234">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="ae5ba-234">Hierarchical configuration data</span></span>

<span data-ttu-id="ae5ba-235">La API de configuración lee los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-235">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="ae5ba-236">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="ae5ba-236">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="ae5ba-237">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-237">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-238">La mejor manera de leer datos de configuración jerárquica es usar el patrón de opciones.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-238">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="ae5ba-239">Para obtener más información, vea la sección [Enlace de datos de configuración jerárquica](#optpat) en este documento.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-239">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="ae5ba-240">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="ae5ba-241">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-241">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="ae5ba-242">Claves y valores de configuración</span><span class="sxs-lookup"><span data-stu-id="ae5ba-242">Configuration keys and values</span></span>

<span data-ttu-id="ae5ba-243">Las claves de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-243">Configuration keys:</span></span>

* <span data-ttu-id="ae5ba-244">No distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-244">Are case-insensitive.</span></span> <span data-ttu-id="ae5ba-245">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-245">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="ae5ba-246">Si se establece una clave y un valor en más de un proveedor de configuración, se usa el valor del último proveedor agregado.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-246">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="ae5ba-247">Para obtener más información, vea la sección [Configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-247">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="ae5ba-248">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="ae5ba-248">Hierarchical keys</span></span>
  * <span data-ttu-id="ae5ba-249">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-249">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="ae5ba-250">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-250">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="ae5ba-251">Todas las plataformas admiten un carácter de subrayado doble, `__`, que se convierte automáticamente en un signo de dos puntos `:`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-251">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="ae5ba-252">En Azure Key Vault, las claves jerárquicas usan `--` como separador.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-252">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="ae5ba-253">El [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) reemplaza automáticamente `--` con `:` cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-253">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="ae5ba-254"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-254">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ae5ba-255">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#boa).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-255">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="ae5ba-256">Los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-256">Configuration values:</span></span>

* <span data-ttu-id="ae5ba-257">Son cadenas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-257">Are strings.</span></span>
* <span data-ttu-id="ae5ba-258">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-258">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="ae5ba-259">Proveedores de configuración</span><span class="sxs-lookup"><span data-stu-id="ae5ba-259">Configuration providers</span></span>

<span data-ttu-id="ae5ba-260">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-260">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="ae5ba-261">Proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-261">Provider</span></span> | <span data-ttu-id="ae5ba-262">Proporciona la configuración de</span><span class="sxs-lookup"><span data-stu-id="ae5ba-262">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="ae5ba-263">Proveedor de configuración de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-263">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="ae5ba-264">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-264">Azure Key Vault</span></span> |
| [<span data-ttu-id="ae5ba-265">Proveedor de configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="ae5ba-265">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="ae5ba-266">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="ae5ba-266">Azure App Configuration</span></span> |
| [<span data-ttu-id="ae5ba-267">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-267">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="ae5ba-268">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-268">Command-line parameters</span></span> |
| [<span data-ttu-id="ae5ba-269">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-269">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="ae5ba-270">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="ae5ba-270">Custom source</span></span> |
| [<span data-ttu-id="ae5ba-271">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-271">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="ae5ba-272">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-272">Environment variables</span></span> |
| [<span data-ttu-id="ae5ba-273">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-273">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="ae5ba-274">Archivos INI, JSON y XML</span><span class="sxs-lookup"><span data-stu-id="ae5ba-274">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="ae5ba-275">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-275">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="ae5ba-276">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="ae5ba-276">Directory files</span></span> |
| [<span data-ttu-id="ae5ba-277">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-277">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="ae5ba-278">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-278">In-memory collections</span></span> |
| [<span data-ttu-id="ae5ba-279">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-279">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="ae5ba-280">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="ae5ba-280">File in the user profile directory</span></span> |

<span data-ttu-id="ae5ba-281">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-281">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="ae5ba-282">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-282">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="ae5ba-283">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-283">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="ae5ba-284">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="ae5ba-284">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="ae5ba-285">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-285">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="ae5ba-286">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-286">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="ae5ba-287">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-287">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="ae5ba-288">Una práctica común es agregar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-288">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="ae5ba-289">La secuencia de proveedores anterior se usa en la [configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-289">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="ae5ba-290">Prefijos de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="ae5ba-290">Connection string prefixes</span></span>

<span data-ttu-id="ae5ba-291">La API de configuración tiene reglas de procesamiento especiales para cuatro variables de entorno de cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-291">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="ae5ba-292">Estas cadenas de conexión están implicadas en la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-292">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="ae5ba-293">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación con la [configuración predeterminada](#default) o cuando no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-293">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="ae5ba-294">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="ae5ba-294">Connection string prefix</span></span> | <span data-ttu-id="ae5ba-295">Proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-295">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="ae5ba-296">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="ae5ba-296">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="ae5ba-297">MySQL</span><span class="sxs-lookup"><span data-stu-id="ae5ba-297">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="ae5ba-298">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="ae5ba-298">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="ae5ba-299">SQL Server</span><span class="sxs-lookup"><span data-stu-id="ae5ba-299">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="ae5ba-300">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-300">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="ae5ba-301">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-301">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="ae5ba-302">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-302">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="ae5ba-303">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-303">Environment variable key</span></span> | <span data-ttu-id="ae5ba-304">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="ae5ba-304">Converted configuration key</span></span> | <span data-ttu-id="ae5ba-305">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-305">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-306">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-306">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-307">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-308">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-308">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-309">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-309">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-310">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-310">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-311">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-311">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-312">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-312">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="ae5ba-313">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-313">File configuration provider</span></span>

<span data-ttu-id="ae5ba-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="ae5ba-315">Los siguientes proveedores de configuración se derivan de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-315">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="ae5ba-316">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="ae5ba-316">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="ae5ba-317">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="ae5ba-317">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="ae5ba-318">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="ae5ba-318">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="ae5ba-319">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="ae5ba-319">INI configuration provider</span></span>

<span data-ttu-id="ae5ba-320"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-320">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-321">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="ae5ba-322">En el código anterior, se reemplaza la configuración de los archivos *MyIniConfig.ini* y *MyIniConfig*.`Environment`.*ini* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-322">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="ae5ba-323">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-323">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="ae5ba-324">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-324">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ae5ba-325">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="ae5ba-326">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="ae5ba-327">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="ae5ba-327">JSON configuration provider</span></span>

<span data-ttu-id="ae5ba-328"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-328">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="ae5ba-329">Las sobrecargas pueden especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-329">Overloads can specify:</span></span>

* <span data-ttu-id="ae5ba-330">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-330">Whether the file is optional.</span></span>
* <span data-ttu-id="ae5ba-331">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-331">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="ae5ba-332">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-332">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="ae5ba-333">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-333">The preceding code:</span></span>

* <span data-ttu-id="ae5ba-334">Configura el proveedor de configuración JSON para que cargue el archivo *MyConfig.json* con las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-334">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="ae5ba-335">`optional: true`: el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-335">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="ae5ba-336">`reloadOnChange: true`: el archivo se recarga cuando se guardan los cambios.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-336">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="ae5ba-337">Lee los [proveedores de configuración predeterminados](#default) antes que el archivo *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-337">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="ae5ba-338">La configuración del archivo *MyConfig.json* invalida la de los proveedores de configuración predeterminados, incluidos el [proveedor de configuración de variables de entorno](#evcp) y el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-338">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ae5ba-339">Normalmente \***no** _ querrá que un archivo JSON personalizado invalide los valores establecidos en el [proveedor de configuración de variables de entorno](#evcp) ni en el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-339">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ae5ba-340">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-340">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="ae5ba-341">En el código anterior, la configuración de los archivos _MyConfig.json\* y *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-341">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="ae5ba-342">Invalida la configuración de los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-342">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="ae5ba-343">Es reemplazada por la configuración del [proveedor de configuración de variables de entorno](#evcp) y del [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-343">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ae5ba-344">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-344">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="ae5ba-345">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-345">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="ae5ba-346">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="ae5ba-346">XML configuration provider</span></span>

<span data-ttu-id="ae5ba-347"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-347">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-348">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="ae5ba-349">En el código anterior, se reemplaza la configuración de los archivos *MyXMLFile.xml* y *MyXMLFile*.`Environment`.*xml* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-349">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="ae5ba-350">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-350">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="ae5ba-351">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-351">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ae5ba-352">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="ae5ba-353">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-354">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-354">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="ae5ba-355">El código siguiente lee el archivo de configuración anterior y muestra las claves y los valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-355">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-356">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-356">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="ae5ba-357">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-357">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ae5ba-358">key:attribute</span><span class="sxs-lookup"><span data-stu-id="ae5ba-358">key:attribute</span></span>
* <span data-ttu-id="ae5ba-359">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="ae5ba-359">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="ae5ba-360">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-360">Key-per-file configuration provider</span></span>

<span data-ttu-id="ae5ba-361"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-361">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="ae5ba-362">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-362">The key is the file name.</span></span> <span data-ttu-id="ae5ba-363">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-363">The value contains the file's contents.</span></span> <span data-ttu-id="ae5ba-364">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-364">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="ae5ba-365">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-365">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="ae5ba-366">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-366">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="ae5ba-367">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-367">Overloads permit specifying:</span></span>

* <span data-ttu-id="ae5ba-368">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-368">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="ae5ba-369">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-369">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="ae5ba-370">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-370">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="ae5ba-371">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-371">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="ae5ba-372">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-372">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="ae5ba-373">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-373">Memory configuration provider</span></span>

<span data-ttu-id="ae5ba-374"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-374">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="ae5ba-375">El código siguiente agrega una colección en memoria al sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-375">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="ae5ba-376">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra las opciones de configuración anteriores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-376">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-377">En el código anterior, `config.AddInMemoryCollection(Dict)` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-377">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="ae5ba-378">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-378">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="ae5ba-379">Consulte la sección [Enlace de matrices](#boa) para ver otro ejemplo que usa `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-379">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="ae5ba-380">GetValue</span><span class="sxs-lookup"><span data-stu-id="ae5ba-380">GetValue</span></span>

<span data-ttu-id="ae5ba-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-382">En el código anterior, si `NumberKey` no se encuentra en la configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-382">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="ae5ba-383">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="ae5ba-383">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="ae5ba-384">Para los ejemplos que aparecen a continuación, considere el siguiente archivo *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-384">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="ae5ba-385">El código siguiente agrega *MySubsection.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-385">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="ae5ba-386">GetSection</span><span class="sxs-lookup"><span data-stu-id="ae5ba-386">GetSection</span></span>

<span data-ttu-id="ae5ba-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) devuelve una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="ae5ba-388">El código siguiente devuelve los valores de `section1`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-388">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-389">El código siguiente devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-389">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-390">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-390">`GetSection` never returns `null`.</span></span> <span data-ttu-id="ae5ba-391">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-391">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="ae5ba-392">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-392">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="ae5ba-393">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="ae5ba-394">GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="ae5ba-394">GetChildren and Exists</span></span>

<span data-ttu-id="ae5ba-395">El código siguiente llama a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) y devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-395">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-396">El código anterior llama a [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para comprobar que la sección existe:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-396">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="ae5ba-397">Enlace de matrices</span><span class="sxs-lookup"><span data-stu-id="ae5ba-397">Bind an array</span></span>

<span data-ttu-id="ae5ba-398">El método [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) permite enlazar matrices a objetos mediante el uso de los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-398">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ae5ba-399">Cualquier formato de matriz que exponga un segmento de clave numérica es capaz de enlazar una matriz a una matriz de clase [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-399">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="ae5ba-400">Tenga en cuenta este archivo *MyArray.json* de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="ae5ba-400">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="ae5ba-401">El código siguiente agrega *MyArray.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-401">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="ae5ba-402">El código siguiente lee la configuración y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-402">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-403">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="ae5ba-404">En el resultado anterior, el índice 3 tiene el valor `value40`, que corresponde a `"4": "value40",` en *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-404">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="ae5ba-405">Los índices de matriz enlazados son continuos y no están enlazados al índice de la clave de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-405">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="ae5ba-406">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en objetos enlazados.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-406">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="ae5ba-407">El código siguiente carga la configuración de `array:entries` con el método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-407">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="ae5ba-408">El código siguiente lee la configuración de `arrayDict` `Dictionary` y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-408">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-409">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-409">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="ae5ba-410">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-410">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="ae5ba-411">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-411">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="ae5ba-412">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-412">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="ae5ba-413">Cualquier proveedor de configuración que lea el par clave-valor del índice &num;3 puede proporcionar el elemento de configuración omitido para el índice &num;3 antes del enlace a la instancia `ArrayExample`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-413">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="ae5ba-414">Fíjese en el siguiente archivo *Value3.json* de la descarga de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-414">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="ae5ba-415">En el código siguiente se incluye la configuración para *Value3.json* y `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-415">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="ae5ba-416">El código siguiente lee la configuración anterior y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-416">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-417">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-417">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="ae5ba-418">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-418">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="ae5ba-419">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-419">Custom configuration provider</span></span>

<span data-ttu-id="ae5ba-420">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-420">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="ae5ba-421">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-421">The provider has the following characteristics:</span></span>

* <span data-ttu-id="ae5ba-422">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-422">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="ae5ba-423">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-423">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="ae5ba-424">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-424">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="ae5ba-425">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-425">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="ae5ba-426">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-426">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="ae5ba-427">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-427">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="ae5ba-428">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-428">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ae5ba-429">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-429">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ae5ba-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ae5ba-431">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-431">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ae5ba-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ae5ba-433">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-433">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ae5ba-434">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-434">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="ae5ba-435">Puesto que las [claves de configuración no distinguen entre mayúsculas y minúsculas](#keys), el diccionario empleado para iniciar la base de datos se crea con el comparador que tampoco hace tal distinción ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-435">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="ae5ba-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ae5ba-437">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-437">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ae5ba-438">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-438">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ae5ba-439">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-439">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="ae5ba-440">Acceso a la configuración en métodos Startup</span><span class="sxs-lookup"><span data-stu-id="ae5ba-440">Access configuration in Startup</span></span>

<span data-ttu-id="ae5ba-441">En el código siguiente se muestran los datos de configuración de los métodos `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-441">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="ae5ba-442">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-442">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="ae5ba-443">Acceso a la configuración en Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ae5ba-443">Access configuration in Razor Pages</span></span>

<span data-ttu-id="ae5ba-444">En el código siguiente se muestran los datos de configuración en una página de RazorPages:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-444">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="ae5ba-445">En el siguiente código se agrega `MyOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-445">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="ae5ba-446">En el marcado siguiente se usa la directiva [`@inject`](xref:mvc/views/razor#inject) Razor para resolver y mostrar los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-446">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="ae5ba-447">Acceso a la configuración en un archivo de vista de MVC</span><span class="sxs-lookup"><span data-stu-id="ae5ba-447">Access configuration in a MVC view file</span></span>

<span data-ttu-id="ae5ba-448">En el código siguiente se muestran los datos de configuración de una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-448">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="ae5ba-449">Configurar opciones con un delegado</span><span class="sxs-lookup"><span data-stu-id="ae5ba-449">Configure options with a delegate</span></span>

<span data-ttu-id="ae5ba-450">Las opciones configuradas en un delegado invalidan los valores establecidos en los proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-450">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="ae5ba-451">La configuración de opciones con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-451">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="ae5ba-452">En el código siguiente, se agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-452">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ae5ba-453">Usa un delegado para configurar los valores de `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-453">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="ae5ba-454">En el código siguiente se muestran los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-454">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="ae5ba-455">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appsettings.json* y, luego, se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-455">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="ae5ba-456">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="ae5ba-456">Host versus app configuration</span></span>

<span data-ttu-id="ae5ba-457">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="ae5ba-458">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ae5ba-459">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="ae5ba-460">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="ae5ba-461">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="ae5ba-462">Configuración predeterminada del host</span><span class="sxs-lookup"><span data-stu-id="ae5ba-462">Default host configuration</span></span>

<span data-ttu-id="ae5ba-463">Para obtener más información sobre la configuración predeterminada al usar el [host de web](xref:fundamentals/host/web-host), vea la [versión de este tema para ASP.NET Core 2.2](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="ae5ba-464">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ae5ba-465">Las variables de entorno con el prefijo `DOTNET_` (por ejemplo, `DOTNET_ENVIRONMENT`) mediante el [proveedor de configuración de variables de entorno](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="ae5ba-466">El prefijo (`DOTNET_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ae5ba-467">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ae5ba-468">La configuración predeterminada del host de web se ha establecido (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="ae5ba-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="ae5ba-469">Kestrel se usa como el servidor web y se ha configurado mediante los proveedores de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="ae5ba-470">Agregar el middleware de filtrado de host</span><span class="sxs-lookup"><span data-stu-id="ae5ba-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="ae5ba-471">Agregue el middleware de encabezados reenviados si la variable de entorno `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="ae5ba-472">Habilite la integración con IIS.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="ae5ba-473">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="ae5ba-473">Other configuration</span></span>

<span data-ttu-id="ae5ba-474">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="ae5ba-475">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="ae5ba-476">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="ae5ba-477">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="ae5ba-478">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="ae5ba-479">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="ae5ba-480">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-480">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="ae5ba-481">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="ae5ba-482">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="ae5ba-483">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ae5ba-484">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae5ba-485">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ae5ba-485">Additional resources</span></span>

* [<span data-ttu-id="ae5ba-486">Configuración del código fuente</span><span class="sxs-lookup"><span data-stu-id="ae5ba-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ae5ba-487">La configuración de la aplicación en ASP.NET Core se basa en pares clave-valor establecidos por *proveedores de configuración*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="ae5ba-488">Los proveedores de configuración leen los datos de configuración en los pares clave-valor de distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="ae5ba-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-489">Azure Key Vault</span></span>
* <span data-ttu-id="ae5ba-490">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="ae5ba-490">Azure App Configuration</span></span>
* <span data-ttu-id="ae5ba-491">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-491">Command-line arguments</span></span>
* <span data-ttu-id="ae5ba-492">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="ae5ba-493">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="ae5ba-493">Directory files</span></span>
* <span data-ttu-id="ae5ba-494">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-494">Environment variables</span></span>
* <span data-ttu-id="ae5ba-495">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-495">In-memory .NET objects</span></span>
* <span data-ttu-id="ae5ba-496">Archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="ae5ba-496">Settings files</span></span>

<span data-ttu-id="ae5ba-497">Los paquetes de configuración para escenarios de proveedor de configuración común ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) se incluyen en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ae5ba-498">Los ejemplos de código que siguen y en la aplicación de ejemplo utilizan el espacio de nombres <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="ae5ba-499">El *patrón de opciones* es una extensión de los conceptos de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="ae5ba-500">Las opciones usan clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="ae5ba-501">Para obtener más información, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ae5ba-502">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ae5ba-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="ae5ba-503">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="ae5ba-503">Host versus app configuration</span></span>

<span data-ttu-id="ae5ba-504">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="ae5ba-505">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ae5ba-506">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="ae5ba-507">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="ae5ba-508">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="ae5ba-509">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="ae5ba-509">Other configuration</span></span>

<span data-ttu-id="ae5ba-510">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="ae5ba-511">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="ae5ba-512">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="ae5ba-513">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="ae5ba-514">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="ae5ba-515">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="ae5ba-516">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="ae5ba-517">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-517">Default configuration</span></span>

<span data-ttu-id="ae5ba-518">Las aplicaciones web basadas en las plantillas de [dotnet new](/dotnet/core/tools/dotnet-new) de ASP.NET Core llaman a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> al crear un host.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="ae5ba-519">`CreateDefaultBuilder` proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="ae5ba-520">El contenido siguiente es válido para las aplicaciones que usen el [host de web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ae5ba-521">Para obtener más información sobre la configuración predeterminada al usar el [host genérico](xref:fundamentals/host/generic-host), vea la [versión más reciente de ese tema](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="ae5ba-522">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ae5ba-523">Variables de entorno prefijadas con `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`) con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="ae5ba-524">El prefijo (`ASPNETCORE_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ae5ba-525">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ae5ba-526">La configuración de la aplicación la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="ae5ba-527">*appsettings.json* con el [proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ae5ba-528">*appsettings.{Entorno}.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ae5ba-529">[Administrador de secretos](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ae5ba-530">Variables de entorno con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="ae5ba-531">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="ae5ba-532">Seguridad</span><span class="sxs-lookup"><span data-stu-id="ae5ba-532">Security</span></span>

<span data-ttu-id="ae5ba-533">Adopte los procedimientos siguientes para proteger los datos de configuración confidenciales:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="ae5ba-534">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="ae5ba-535">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="ae5ba-536">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="ae5ba-537">Para obtener más información, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="ae5ba-538"><xref:security/app-secrets>: incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-538"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="ae5ba-539">El Administrador de secretos usa el proveedor de configuración de archivo para almacenar secretos de usuario en un archivo JSON del sistema local.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="ae5ba-540">El proveedor de configuración de archivo se describe más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="ae5ba-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="ae5ba-542">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="ae5ba-543">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="ae5ba-543">Hierarchical configuration data</span></span>

<span data-ttu-id="ae5ba-544">La API de configuración es capaz de mantener los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="ae5ba-545">En el siguiente archivo JSON, hay cuatro claves en una estructura jerárquica de dos secciones:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="ae5ba-546">Cuando el archivo se lee en la configuración, se crean claves únicas para mantener la estructura de datos jerárquicos original del origen de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="ae5ba-547">Las secciones y claves se disminuyen con el uso de dos puntos (`:`) para mantener la estructura original:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="ae5ba-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-548">section0:key0</span></span>
* <span data-ttu-id="ae5ba-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-549">section0:key1</span></span>
* <span data-ttu-id="ae5ba-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-550">section1:key0</span></span>
* <span data-ttu-id="ae5ba-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-551">section1:key1</span></span>

<span data-ttu-id="ae5ba-552">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="ae5ba-553">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="ae5ba-554">Convenciones</span><span class="sxs-lookup"><span data-stu-id="ae5ba-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="ae5ba-555">Orígenes y proveedores</span><span class="sxs-lookup"><span data-stu-id="ae5ba-555">Sources and providers</span></span>

<span data-ttu-id="ae5ba-556">Al iniciar la aplicación, los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="ae5ba-557">Los proveedores de configuración que implementan la detección de cambios tienen la capacidad de volver a cargar la configuración cuando se cambia una configuración subyacente.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="ae5ba-558">Por ejemplo, el proveedor de configuración de archivos (descrito más adelante en este tema) y el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) implementan la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="ae5ba-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponible en el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ae5ba-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> se puede insertar en Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obtener la configuración de la clase.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="ae5ba-561">En los ejemplos siguientes, se usa el campo `_config` para tener acceso a los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="ae5ba-562">Los proveedores de configuración no pueden usar la inserción de dependencias, porque no está disponible cuando el host los configura.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="ae5ba-563">Teclas</span><span class="sxs-lookup"><span data-stu-id="ae5ba-563">Keys</span></span>

<span data-ttu-id="ae5ba-564">Las claves de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="ae5ba-565">Las claves no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-565">Keys are case-insensitive.</span></span> <span data-ttu-id="ae5ba-566">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="ae5ba-567">Si los mismos o distintos proveedores de configuración establecen un valor para la misma clave, el último valor establecido en la clave es el valor que se usa.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="ae5ba-568">Para más información sobre las claves JSON duplicadas, vea [este problema de GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-568">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="ae5ba-569">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="ae5ba-569">Hierarchical keys</span></span>
  * <span data-ttu-id="ae5ba-570">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-570">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="ae5ba-571">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-571">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="ae5ba-572">Todas las plataformas admiten un carácter de subrayado doble (`__`), que se convierte automáticamente en un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-572">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="ae5ba-573">En Azure Key Vault, las claves jerárquicas usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-573">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ae5ba-574">Escriba código para reemplazar los guiones por dos puntos cuando los secretos se carguen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-574">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="ae5ba-575"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-575">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ae5ba-576">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-576">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="ae5ba-577">Valores</span><span class="sxs-lookup"><span data-stu-id="ae5ba-577">Values</span></span>

<span data-ttu-id="ae5ba-578">Los valores de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-578">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="ae5ba-579">Los valores son cadenas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-579">Values are strings.</span></span>
* <span data-ttu-id="ae5ba-580">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-580">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="ae5ba-581">Proveedores</span><span class="sxs-lookup"><span data-stu-id="ae5ba-581">Providers</span></span>

<span data-ttu-id="ae5ba-582">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-582">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="ae5ba-583">Proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-583">Provider</span></span> | <span data-ttu-id="ae5ba-584">Proporciona la configuración de&hellip;</span><span class="sxs-lookup"><span data-stu-id="ae5ba-584">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="ae5ba-585">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-585">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="ae5ba-586">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-586">Azure Key Vault</span></span> |
| <span data-ttu-id="ae5ba-587">[Proveedor de Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-587">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="ae5ba-588">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="ae5ba-588">Azure App Configuration</span></span> |
| [<span data-ttu-id="ae5ba-589">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-589">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="ae5ba-590">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-590">Command-line parameters</span></span> |
| [<span data-ttu-id="ae5ba-591">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-591">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="ae5ba-592">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="ae5ba-592">Custom source</span></span> |
| [<span data-ttu-id="ae5ba-593">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-593">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="ae5ba-594">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-594">Environment variables</span></span> |
| [<span data-ttu-id="ae5ba-595">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-595">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="ae5ba-596">Archivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-596">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="ae5ba-597">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-597">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="ae5ba-598">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="ae5ba-598">Directory files</span></span> |
| [<span data-ttu-id="ae5ba-599">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-599">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="ae5ba-600">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-600">In-memory collections</span></span> |
| <span data-ttu-id="ae5ba-601">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-601">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="ae5ba-602">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="ae5ba-602">File in the user profile directory</span></span> |

<span data-ttu-id="ae5ba-603">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-603">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="ae5ba-604">En este tema, los proveedores de configuración se describen en orden alfabético y no en el orden en el que el código los organiza.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-604">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="ae5ba-605">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-605">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="ae5ba-606">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-606">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="ae5ba-607">Archivos ( *appsettings.json* y *appsettings.{Environment}.json*, donde `{Environment}` es el entorno de hospedaje actual de la aplicación)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-607">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="ae5ba-608">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ae5ba-608">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="ae5ba-609">[Secretos de usuario (administrador de secretos)](xref:security/app-secrets) (solo para entornos de desarrollo)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-609">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="ae5ba-610">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-610">Environment variables</span></span>
1. <span data-ttu-id="ae5ba-611">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-611">Command-line arguments</span></span>

<span data-ttu-id="ae5ba-612">Una práctica común es colocar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-612">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="ae5ba-613">La secuencia de proveedores anterior se usa cuando se inicializa un nuevo generador de host con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-613">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae5ba-614">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-614">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="ae5ba-615">Configurar el generador de host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="ae5ba-615">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="ae5ba-616">Para configurar el generador de host, realice una llamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> en el generador de host con la configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-616">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="ae5ba-617">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ae5ba-617">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ae5ba-618">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar los proveedores de configuración de la aplicación además de aquellos que `CreateDefaultBuilder` agrega automáticamente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-618">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="ae5ba-619">Reemplazar la configuración anterior con argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-619">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="ae5ba-620">Para proporcionar configuración de aplicaciones que se pueda reemplazar por argumentos de la línea de comandos, llame a `AddCommandLine` en último lugar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-620">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="ae5ba-621">Quitar proveedores agregados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="ae5ba-621">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="ae5ba-622">Para quitar los proveedores agregados por `CreateDefaultBuilder`, llame primero a [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) en [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="ae5ba-622">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="ae5ba-623">Usar la configuración durante el inicio de la aplicación</span><span class="sxs-lookup"><span data-stu-id="ae5ba-623">Consume configuration during app startup</span></span>

<span data-ttu-id="ae5ba-624">La configuración proporcionada a la aplicación en `ConfigureAppConfiguration` está disponible durante el inicio de la aplicación, incluido `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-624">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ae5ba-625">Para obtener más información, vea la sección [Acceso a la configuración durante el inicio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-625">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="ae5ba-626">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-626">Command-line Configuration Provider</span></span>

<span data-ttu-id="ae5ba-627"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de pares clave-valor de argumento de la línea de comandos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-627">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-628">Para activar la configuración de línea de comandos, se llama al método de extensión <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-628">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ae5ba-629">`AddCommandLine` se llama automáticamente al llamar a `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-629">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="ae5ba-630">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-630">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ae5ba-631">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-631">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ae5ba-632">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-632">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ae5ba-633">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-633">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ae5ba-634">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-634">Environment variables.</span></span>

<span data-ttu-id="ae5ba-635">`CreateDefaultBuilder` agrega el proveedor de configuración de línea de comandos al final.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-635">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="ae5ba-636">Los argumentos de la línea de comandos que se pasan en tiempo de ejecución invalidan la configuración establecida por los otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-636">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="ae5ba-637">`CreateDefaultBuilder` actúa cuando se construye el host.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-637">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="ae5ba-638">Por tanto, la configuración de línea de comandos activada por `CreateDefaultBuilder` puede afectar la manera en que se configura el host.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-638">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="ae5ba-639">Para las aplicaciones basadas en plantillas de ASP.NET Core, `CreateDefaultBuilder` ya realiza una llamada a `AddCommandLine`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-639">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae5ba-640">Para agregar proveedores de configuración adicionales y mantener la capacidad de reemplazar la configuración de sus proveedores con argumentos de la línea de comandos, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddCommandLine` en último lugar.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-640">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="ae5ba-641">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-641">**Example**</span></span>

<span data-ttu-id="ae5ba-642">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-642">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="ae5ba-643">Abra un símbolo del sistema en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-643">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="ae5ba-644">Proporcione un argumento de la línea de comandos para el comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-644">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="ae5ba-645">Una vez que se ejecuta la aplicación, abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-645">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ae5ba-646">Observe que la salida contiene el par clave-valor para el argumento de línea de comandos de configuración proporcionado a `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-646">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="ae5ba-647">Argumentos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-647">Arguments</span></span>

<span data-ttu-id="ae5ba-648">El valor debe seguir a un signo igual (`=`) o la clave debe tener un prefijo (`--` o `/`) cuando el valor siga a un espacio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-648">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="ae5ba-649">El valor no es obligatorio si se usa un signo igual (por ejemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-649">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="ae5ba-650">Prefijo de la clave</span><span class="sxs-lookup"><span data-stu-id="ae5ba-650">Key prefix</span></span>               | <span data-ttu-id="ae5ba-651">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-651">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="ae5ba-652">Sin prefijo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-652">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="ae5ba-653">Dos guiones (`--`)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-653">Two dashes (`--`)</span></span>        | <span data-ttu-id="ae5ba-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="ae5ba-655">Barra diagonal (`/`)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-655">Forward slash (`/`)</span></span>      | <span data-ttu-id="ae5ba-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="ae5ba-657">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan un signo igual con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-657">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="ae5ba-658">Comandos de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-658">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="ae5ba-659">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="ae5ba-659">Switch mappings</span></span>

<span data-ttu-id="ae5ba-660">Las asignaciones de modificador admiten la lógica de sustitución de nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-660">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="ae5ba-661">Al crear manualmente la configuración con <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-661">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="ae5ba-662">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-662">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="ae5ba-663">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario (el reemplazo de la clave) para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-663">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="ae5ba-664">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-664">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="ae5ba-665">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-665">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="ae5ba-666">Los modificadores deben empezar por un guion (`-`) o guion doble (`--`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-666">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="ae5ba-667">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-667">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="ae5ba-668">Cree un diccionario de asignaciones de modificador.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-668">Create a switch mappings dictionary.</span></span> <span data-ttu-id="ae5ba-669">En el ejemplo siguiente, se crean dos asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-669">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="ae5ba-670">Al compilar el host, llame a `AddCommandLine` con el diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-670">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="ae5ba-671">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-671">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="ae5ba-672">En la llamada de `AddCommandLine` del método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-672">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae5ba-673">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino que permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-673">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="ae5ba-674">Después de crear el diccionario de asignaciones de modificador, contiene los datos que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-674">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="ae5ba-675">Clave</span><span class="sxs-lookup"><span data-stu-id="ae5ba-675">Key</span></span>       | <span data-ttu-id="ae5ba-676">Valor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-676">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="ae5ba-677">Si las claves de asignación de conmutador se usan al iniciar la aplicación, la configuración recibe el valor de configuración en la clave que el diccionario suministra:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-677">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="ae5ba-678">Una vez que se ejecuta el comando anterior, la configuración contiene los valores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-678">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="ae5ba-679">Key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-679">Key</span></span>               | <span data-ttu-id="ae5ba-680">Valor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-680">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="ae5ba-681">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-681">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="ae5ba-682"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de pares clave-valor de variables de entorno en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-682">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-683">Para activar la configuración de variables de entorno, llame al método de extensión <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-683">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ae5ba-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permite establecer variables de entorno en Azure Portal que pueden invalidar la configuración de la aplicación mediante el proveedor de configuración de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="ae5ba-685">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-685">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="ae5ba-686">`AddEnvironmentVariables` se usa para cargar variables de entorno con el prefijo `ASPNETCORE_` para la [configuración de host](#host-versus-app-configuration) al inicializar un nuevo generador de host con el [host de web](xref:fundamentals/host/web-host) y al llamar a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-686">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="ae5ba-687">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-687">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ae5ba-688">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-688">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ae5ba-689">Configuración de la aplicación desde variables de entorno sin prefijo mediante la llamada a `AddEnvironmentVariables` sin prefijo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-689">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="ae5ba-690">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-690">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ae5ba-691">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-691">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ae5ba-692">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-692">Command-line arguments.</span></span>

<span data-ttu-id="ae5ba-693">El proveedor de configuración de variables de entorno se llama una vez establecida la configuración desde los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-693">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="ae5ba-694">Llamar al proveedor en esta posición permite que la lectura de las variables de entorno en tiempo de ejecución invaliden la configuración establecida por los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-694">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="ae5ba-695">Para proporcionar la configuración de la aplicación desde variables de entorno adicionales, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddEnvironmentVariables` con el prefijo:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-695">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="ae5ba-696">Llame a `AddEnvironmentVariables` en último lugar para permitir que las variables de entorno con el prefijo especificado invaliden los valores de otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-696">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="ae5ba-697">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-697">**Example**</span></span>

<span data-ttu-id="ae5ba-698">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-698">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="ae5ba-699">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-699">Run the sample app.</span></span> <span data-ttu-id="ae5ba-700">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-700">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ae5ba-701">Observe que el resultado contiene el par clave y valor para la variable de entorno `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-701">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="ae5ba-702">El valor refleja el entorno en el que se ejecuta la aplicación, habitualmente `Development` cuando se ejecuta de manera local.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-702">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="ae5ba-703">Para mantener un número adecuado de variables de entorno en la lista representada por la aplicación, la aplicación filtrará las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-703">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="ae5ba-704">Vea el archivo *Pages/Index.cshtml.cs* de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-704">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="ae5ba-705">Para exponer todas las variables de entorno disponibles para la aplicación, cambie `FilteredConfiguration` en *Pages/Index.cshtml.cs* por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-705">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="ae5ba-706">Prefijos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-706">Prefixes</span></span>

<span data-ttu-id="ae5ba-707">Las variables de entorno que se cargan en la configuración de la aplicación se filtran cuando se proporciona un prefijo para el método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-707">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="ae5ba-708">Por ejemplo, para filtrar las variables de entorno en el prefijo `CUSTOM_`, suministre el prefijo para el proveedor de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-708">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="ae5ba-709">El prefijo se quita cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-709">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="ae5ba-710">Al crear el generador de host, las variables de entorno proporcionan la configuración del host.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-710">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="ae5ba-711">Para obtener más información sobre el prefijo usado para estas variables de entorno, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-711">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ae5ba-712">**Prefijos de cadena de conexión**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-712">**Connection string prefixes**</span></span>

<span data-ttu-id="ae5ba-713">La API de configuración tiene reglas de procesamiento especial para cuatro variables de entorno de cadena de conexión relacionadas con la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-713">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="ae5ba-714">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación si no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-714">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="ae5ba-715">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="ae5ba-715">Connection string prefix</span></span> | <span data-ttu-id="ae5ba-716">Proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-716">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="ae5ba-717">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="ae5ba-717">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="ae5ba-718">MySQL</span><span class="sxs-lookup"><span data-stu-id="ae5ba-718">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="ae5ba-719">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="ae5ba-719">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="ae5ba-720">SQL Server</span><span class="sxs-lookup"><span data-stu-id="ae5ba-720">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="ae5ba-721">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-721">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="ae5ba-722">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-722">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="ae5ba-723">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-723">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="ae5ba-724">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="ae5ba-724">Environment variable key</span></span> | <span data-ttu-id="ae5ba-725">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="ae5ba-725">Converted configuration key</span></span> | <span data-ttu-id="ae5ba-726">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-726">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-727">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-727">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-728">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-729">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-729">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-730">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-731">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-731">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ae5ba-732">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-732">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ae5ba-733">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-733">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="ae5ba-734">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-734">**Example**</span></span>

<span data-ttu-id="ae5ba-735">Se crea una variable de entorno de una cadena de conexión personalizada en el servidor:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-735">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="ae5ba-736">Nombre: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-736">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="ae5ba-737">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-737">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="ae5ba-738">Si `IConfiguration` se inserta y se asigna a un campo denominado `_config`, se lee el valor siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-738">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="ae5ba-739">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-739">File Configuration Provider</span></span>

<span data-ttu-id="ae5ba-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="ae5ba-741">Los proveedores de configuración siguientes están dedicados a determinados tipos de archivo:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-741">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="ae5ba-742">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="ae5ba-742">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="ae5ba-743">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="ae5ba-743">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="ae5ba-744">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="ae5ba-744">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="ae5ba-745">Proveedor de configuración de INI</span><span class="sxs-lookup"><span data-stu-id="ae5ba-745">INI Configuration Provider</span></span>

<span data-ttu-id="ae5ba-746"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-746">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-747">Para activar la configuración de archivo INI, llame al método de extensión <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-747">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ae5ba-748">Los dos puntos se pueden usar como un delimitador de sección en la configuración de archivo INI.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-748">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="ae5ba-749">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-749">Overloads permit specifying:</span></span>

* <span data-ttu-id="ae5ba-750">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-750">Whether the file is optional.</span></span>
* <span data-ttu-id="ae5ba-751">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-751">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ae5ba-752"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-752">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ae5ba-753">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-753">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ae5ba-754">Un ejemplo genérico de un archivo de configuración INI:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-754">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="ae5ba-755">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-755">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ae5ba-756">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-756">section0:key0</span></span>
* <span data-ttu-id="ae5ba-757">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-757">section0:key1</span></span>
* <span data-ttu-id="ae5ba-758">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-758">section1:subsection:key</span></span>
* <span data-ttu-id="ae5ba-759">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-759">section2:subsection0:key</span></span>
* <span data-ttu-id="ae5ba-760">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-760">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="ae5ba-761">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="ae5ba-761">JSON Configuration Provider</span></span>

<span data-ttu-id="ae5ba-762"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-762">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="ae5ba-763">Para activar la configuración de archivo JSON, llame al método de extensión <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-763">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ae5ba-764">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-764">Overloads permit specifying:</span></span>

* <span data-ttu-id="ae5ba-765">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-765">Whether the file is optional.</span></span>
* <span data-ttu-id="ae5ba-766">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-766">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ae5ba-767"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-767">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ae5ba-768">Se llama automáticamente a `AddJsonFile` dos veces cuando un nuevo generador de host se inicializa con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-768">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ae5ba-769">Se llama al método para cargar la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-769">The method is called to load configuration from:</span></span>

* <span data-ttu-id="ae5ba-770">*appsettings.json* : Este archivo se lee primero.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-770">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="ae5ba-771">La versión del entorno del archivo puede invalidar los valores que proporciona el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-771">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="ae5ba-772">*appsettings.{Environment}.json*: La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-772">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="ae5ba-773">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-773">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ae5ba-774">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-774">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ae5ba-775">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-775">Environment variables.</span></span>
* <span data-ttu-id="ae5ba-776">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-776">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ae5ba-777">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-777">Command-line arguments.</span></span>

<span data-ttu-id="ae5ba-778">El proveedor de configuración de JSON se establece en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-778">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="ae5ba-779">Por tanto, los secretos de usuario, las variables de entorno y los argumentos de la línea de comandos invalidan la configuración establecida por los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-779">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="ae5ba-780">Llame a `ConfigureAppConfiguration` al crear el host para especificar la configuración de la aplicación para archivos distintos de *appsettings.json* y *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-780">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ae5ba-781">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-781">**Example**</span></span>

<span data-ttu-id="ae5ba-782">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-782">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="ae5ba-783">La primera llamada a `AddJsonFile` carga la configuración desde *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="ae5ba-783">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="ae5ba-784">La segunda llamada a `AddJsonFile` carga la configuración desde *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-784">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="ae5ba-785">Para *appsettings.Development.json* en la aplicación de ejemplo, se carga el siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-785">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="ae5ba-786">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-786">Run the sample app.</span></span> <span data-ttu-id="ae5ba-787">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-787">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ae5ba-788">La salida contiene pares clave-valor para la configuración basada en el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-788">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="ae5ba-789">El nivel de registro de la clave `Logging:LogLevel:Default` es `Debug` al ejecutar la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-789">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="ae5ba-790">Vuelva a ejecutar la aplicación de ejemplo en el entorno de producción:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-790">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="ae5ba-791">Abra el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-791">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="ae5ba-792">En el perfil de `ConfigurationSample`, cambie el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` por `Production`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-792">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="ae5ba-793">Guarde el archivo y ejecute la aplicación con `dotnet run` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-793">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="ae5ba-794">La configuración de *appsettings.Development.json* ya no invalida la configuración de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="ae5ba-794">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="ae5ba-795">El nivel de registro de la clave `Logging:LogLevel:Default` es `Warning`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-795">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="ae5ba-796">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="ae5ba-796">XML Configuration Provider</span></span>

<span data-ttu-id="ae5ba-797"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-797">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="ae5ba-798">Para activar la configuración de archivo XML, llame al método de extensión <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-798">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ae5ba-799">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-799">Overloads permit specifying:</span></span>

* <span data-ttu-id="ae5ba-800">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-800">Whether the file is optional.</span></span>
* <span data-ttu-id="ae5ba-801">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-801">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ae5ba-802"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-802">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ae5ba-803">El nodo raíz del archivo de configuración se omite cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-803">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="ae5ba-804">No especifique una definición de tipo de documento (DTD) ni el espacio de nombres en el archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-804">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="ae5ba-805">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-805">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ae5ba-806">Los archivos de configuración XML pueden usar distintos nombres de elemento para las secciones repetidas:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-806">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="ae5ba-807">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ae5ba-808">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-808">section0:key0</span></span>
* <span data-ttu-id="ae5ba-809">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-809">section0:key1</span></span>
* <span data-ttu-id="ae5ba-810">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-810">section1:key0</span></span>
* <span data-ttu-id="ae5ba-811">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-811">section1:key1</span></span>

<span data-ttu-id="ae5ba-812">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-812">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="ae5ba-813">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ae5ba-814">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-814">section:section0:key:key0</span></span>
* <span data-ttu-id="ae5ba-815">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-815">section:section0:key:key1</span></span>
* <span data-ttu-id="ae5ba-816">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-816">section:section1:key:key0</span></span>
* <span data-ttu-id="ae5ba-817">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-817">section:section1:key:key1</span></span>

<span data-ttu-id="ae5ba-818">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-818">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="ae5ba-819">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-819">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ae5ba-820">key:attribute</span><span class="sxs-lookup"><span data-stu-id="ae5ba-820">key:attribute</span></span>
* <span data-ttu-id="ae5ba-821">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="ae5ba-821">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="ae5ba-822">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-822">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="ae5ba-823"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-823">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="ae5ba-824">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-824">The key is the file name.</span></span> <span data-ttu-id="ae5ba-825">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-825">The value contains the file's contents.</span></span> <span data-ttu-id="ae5ba-826">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-826">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="ae5ba-827">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-827">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="ae5ba-828">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-828">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="ae5ba-829">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-829">Overloads permit specifying:</span></span>

* <span data-ttu-id="ae5ba-830">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-830">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="ae5ba-831">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-831">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="ae5ba-832">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-832">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="ae5ba-833">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-833">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="ae5ba-834">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="ae5ba-835">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="ae5ba-835">Memory Configuration Provider</span></span>

<span data-ttu-id="ae5ba-836"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-836">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="ae5ba-837">Para activar la configuración de colección en memoria, llame al método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-837">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ae5ba-838">El proveedor de configuración se puede inicializar con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-838">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="ae5ba-839">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-839">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="ae5ba-840">En el ejemplo siguiente, se crea un diccionario de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-840">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="ae5ba-841">El diccionario se usa con una llamada a `AddInMemoryCollection` para proporcionar la configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-841">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="ae5ba-842">GetValue</span><span class="sxs-lookup"><span data-stu-id="ae5ba-842">GetValue</span></span>

<span data-ttu-id="ae5ba-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado que no es de colección.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="ae5ba-844">Una sobrecarga acepta un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-844">An overload accepts a default value.</span></span>

<span data-ttu-id="ae5ba-845">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-845">The following example:</span></span>

* <span data-ttu-id="ae5ba-846">Se extrae el valor de cadena de la configuración con la clave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-846">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="ae5ba-847">Si `NumberKey` no se encuentra en las claves de configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-847">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="ae5ba-848">Se escribe el valor como `int`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-848">Types the value as an `int`.</span></span>
* <span data-ttu-id="ae5ba-849">Se almacena el valor en la propiedad `NumberConfig` para usarlo en la página.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-849">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="ae5ba-850">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="ae5ba-850">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="ae5ba-851">Para los ejemplos siguientes, considere el siguiente archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-851">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="ae5ba-852">Se encuentran cuatro claves en dos secciones, una de las cuales incluye un par de subsecciones:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-852">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="ae5ba-853">Cuando el archivo se lee en la configuración, se crean las siguientes claves jerárquicas únicas para contener los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-853">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="ae5ba-854">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-854">section0:key0</span></span>
* <span data-ttu-id="ae5ba-855">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-855">section0:key1</span></span>
* <span data-ttu-id="ae5ba-856">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-856">section1:key0</span></span>
* <span data-ttu-id="ae5ba-857">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-857">section1:key1</span></span>
* <span data-ttu-id="ae5ba-858">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-858">section2:subsection0:key0</span></span>
* <span data-ttu-id="ae5ba-859">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-859">section2:subsection0:key1</span></span>
* <span data-ttu-id="ae5ba-860">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-860">section2:subsection1:key0</span></span>
* <span data-ttu-id="ae5ba-861">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-861">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="ae5ba-862">GetSection</span><span class="sxs-lookup"><span data-stu-id="ae5ba-862">GetSection</span></span>

<span data-ttu-id="ae5ba-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrae una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="ae5ba-864">Para devolver un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contiene los pares clave-valor en `section1`, llame a `GetSection` y suministre el nombre de la sección:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-864">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="ae5ba-865">`configSection` no tiene ningún valor, solo una clave y una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-865">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="ae5ba-866">De forma similar, para obtener los valores de las claves de `section2:subsection0`, llame a `GetSection` y suministre la ruta de acceso a la sección:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-866">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="ae5ba-867">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-867">`GetSection` never returns `null`.</span></span> <span data-ttu-id="ae5ba-868">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-868">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="ae5ba-869">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-869">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="ae5ba-870">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="ae5ba-871">GetChildren</span><span class="sxs-lookup"><span data-stu-id="ae5ba-871">GetChildren</span></span>

<span data-ttu-id="ae5ba-872">Una llamada a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) en `section2` obtiene una `IEnumerable<IConfigurationSection>` que incluye:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-872">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="ae5ba-873">Existe</span><span class="sxs-lookup"><span data-stu-id="ae5ba-873">Exists</span></span>

<span data-ttu-id="ae5ba-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar si existe una sección de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="ae5ba-875">Dados los datos de ejemplo, `sectionExists` es `false` porque no hay una sección `section2:subsection2` en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-875">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="ae5ba-876">Enlazar a un gráfico de objetos</span><span class="sxs-lookup"><span data-stu-id="ae5ba-876">Bind to an object graph</span></span>

<span data-ttu-id="ae5ba-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> es capaz de enlazar todo un gráfico de objetos POCO.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="ae5ba-878">Al igual que ocurre con el enlace de un objeto simple, solo se enlazan las propiedades públicas de lectura o escritura.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-878">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="ae5ba-879">El ejemplo contiene un modelo `TvShow` cuyo gráfico de objetos incluye las clases `Metadata` y `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="ae5ba-879">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="ae5ba-880">La aplicación de ejemplo tiene un archivo *tvshow.xml* que contiene los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-880">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="ae5ba-881">Configuración está enlazada a todo el gráfico de objetos `TvShow`con el método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-881">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="ae5ba-882">La instancia enlazada se asigna a una propiedad para la representación:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-882">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="ae5ba-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="ae5ba-884">`Get<T>` es más conveniente que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-884">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="ae5ba-885">En el código siguiente se muestra cómo usar `Get<T>` con el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-885">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ae5ba-886">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="ae5ba-886">Bind an array to a class</span></span>

<span data-ttu-id="ae5ba-887">*La aplicación de ejemplo muestra los conceptos que se explican en esta sección.*</span><span class="sxs-lookup"><span data-stu-id="ae5ba-887">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="ae5ba-888"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-888">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ae5ba-889">Cualquier formato de matriz que expone un segmento de clave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) es capaz de enlazar una matriz a una matriz de clase POCO.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-889">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="ae5ba-890">El enlace se proporciona por convención.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-890">Binding is provided by convention.</span></span> <span data-ttu-id="ae5ba-891">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-891">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="ae5ba-892">**Procesamiento de matriz en memoria**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-892">**In-memory array processing**</span></span>

<span data-ttu-id="ae5ba-893">Considere los valores y las claves de configuración que se muestran en esta tabla.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-893">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="ae5ba-894">Key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-894">Key</span></span>             | <span data-ttu-id="ae5ba-895">Valor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-895">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ae5ba-896">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-896">array:entries:0</span></span> | <span data-ttu-id="ae5ba-897">value0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-897">value0</span></span> |
| <span data-ttu-id="ae5ba-898">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-898">array:entries:1</span></span> | <span data-ttu-id="ae5ba-899">value1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-899">value1</span></span> |
| <span data-ttu-id="ae5ba-900">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-900">array:entries:2</span></span> | <span data-ttu-id="ae5ba-901">value2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-901">value2</span></span> |
| <span data-ttu-id="ae5ba-902">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-902">array:entries:4</span></span> | <span data-ttu-id="ae5ba-903">value4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-903">value4</span></span> |
| <span data-ttu-id="ae5ba-904">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="ae5ba-904">array:entries:5</span></span> | <span data-ttu-id="ae5ba-905">value5</span><span class="sxs-lookup"><span data-stu-id="ae5ba-905">value5</span></span> |

<span data-ttu-id="ae5ba-906">Estas claves y valores se cargan en la aplicación de ejemplo a través del proveedor de configuración de memoria:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-906">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="ae5ba-907">La matriz omite un valor de índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-907">The array skips a value for index &num;3.</span></span> <span data-ttu-id="ae5ba-908">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en los objetos enlazados, lo que queda claro cuando se muestra el resultado de enlazar esta matriz a un objeto.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-908">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="ae5ba-909">En la aplicación de ejemplo, hay disponible una clase POCO para almacenar los datos de configuración enlazados:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-909">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="ae5ba-910">Los datos de configuración están enlazados al objeto:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-910">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="ae5ba-911">También se puede usar la sintaxis [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*), lo que genera un código más compacto:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="ae5ba-912">El objeto enlazado, una instancia de `ArrayExample`, recibe los datos de la matriz desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-912">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="ae5ba-913">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-913">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ae5ba-914">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-914">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ae5ba-915">0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-915">0</span></span>                            | <span data-ttu-id="ae5ba-916">value0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-916">value0</span></span>                       |
| <span data-ttu-id="ae5ba-917">1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-917">1</span></span>                            | <span data-ttu-id="ae5ba-918">value1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-918">value1</span></span>                       |
| <span data-ttu-id="ae5ba-919">2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-919">2</span></span>                            | <span data-ttu-id="ae5ba-920">value2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-920">value2</span></span>                       |
| <span data-ttu-id="ae5ba-921">3</span><span class="sxs-lookup"><span data-stu-id="ae5ba-921">3</span></span>                            | <span data-ttu-id="ae5ba-922">value4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-922">value4</span></span>                       |
| <span data-ttu-id="ae5ba-923">4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-923">4</span></span>                            | <span data-ttu-id="ae5ba-924">value5</span><span class="sxs-lookup"><span data-stu-id="ae5ba-924">value5</span></span>                       |

<span data-ttu-id="ae5ba-925">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-925">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="ae5ba-926">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración solo se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-926">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="ae5ba-927">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-927">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="ae5ba-928">El elemento de configuración omitido para el índice &num;3 se puede proporcionar antes del enlace a la instancia `ArrayExample` por cualquier proveedor de configuración que genera el par clave-valor correcto en la configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-928">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="ae5ba-929">Si el ejemplo incluía un proveedor de configuración JSON adicional con el par clave-valor omitido, `ArrayExample.Entries` coincide con la matriz de configuración completa:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-929">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="ae5ba-930">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-930">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="ae5ba-931">En `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-931">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="ae5ba-932">El par clave-valor que se muestra en la tabla se carga en la configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-932">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="ae5ba-933">Clave</span><span class="sxs-lookup"><span data-stu-id="ae5ba-933">Key</span></span>             | <span data-ttu-id="ae5ba-934">Valor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-934">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ae5ba-935">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="ae5ba-935">array:entries:3</span></span> | <span data-ttu-id="ae5ba-936">value3</span><span class="sxs-lookup"><span data-stu-id="ae5ba-936">value3</span></span> |

<span data-ttu-id="ae5ba-937">Si la instancia de clase `ArrayExample` se enlaza después de que el proveedor de configuración JSON incluye la entrada para el índice &num;3, la matriz `ArrayExample.Entries` incluye el valor.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-937">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="ae5ba-938">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-938">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ae5ba-939">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-939">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ae5ba-940">0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-940">0</span></span>                            | <span data-ttu-id="ae5ba-941">value0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-941">value0</span></span>                       |
| <span data-ttu-id="ae5ba-942">1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-942">1</span></span>                            | <span data-ttu-id="ae5ba-943">value1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-943">value1</span></span>                       |
| <span data-ttu-id="ae5ba-944">2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-944">2</span></span>                            | <span data-ttu-id="ae5ba-945">value2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-945">value2</span></span>                       |
| <span data-ttu-id="ae5ba-946">3</span><span class="sxs-lookup"><span data-stu-id="ae5ba-946">3</span></span>                            | <span data-ttu-id="ae5ba-947">value3</span><span class="sxs-lookup"><span data-stu-id="ae5ba-947">value3</span></span>                       |
| <span data-ttu-id="ae5ba-948">4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-948">4</span></span>                            | <span data-ttu-id="ae5ba-949">value4</span><span class="sxs-lookup"><span data-stu-id="ae5ba-949">value4</span></span>                       |
| <span data-ttu-id="ae5ba-950">5</span><span class="sxs-lookup"><span data-stu-id="ae5ba-950">5</span></span>                            | <span data-ttu-id="ae5ba-951">value5</span><span class="sxs-lookup"><span data-stu-id="ae5ba-951">value5</span></span>                       |

<span data-ttu-id="ae5ba-952">**Procesamiento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="ae5ba-952">**JSON array processing**</span></span>

<span data-ttu-id="ae5ba-953">Si un archivo JSON contiene una matriz, se crean claves de configuración para los elementos de la matriz con un índice de sección basado en cero.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-953">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="ae5ba-954">En el siguiente archivo de configuración, `subsection` es una matriz:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-954">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="ae5ba-955">El proveedor de configuración JSON lee los datos de configuración en los siguientes pares clave-valor:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-955">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="ae5ba-956">Key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-956">Key</span></span>                     | <span data-ttu-id="ae5ba-957">Valor</span><span class="sxs-lookup"><span data-stu-id="ae5ba-957">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="ae5ba-958">json_array:key</span><span class="sxs-lookup"><span data-stu-id="ae5ba-958">json_array:key</span></span>          | <span data-ttu-id="ae5ba-959">valueA</span><span class="sxs-lookup"><span data-stu-id="ae5ba-959">valueA</span></span> |
| <span data-ttu-id="ae5ba-960">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-960">json_array:subsection:0</span></span> | <span data-ttu-id="ae5ba-961">valueB</span><span class="sxs-lookup"><span data-stu-id="ae5ba-961">valueB</span></span> |
| <span data-ttu-id="ae5ba-962">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-962">json_array:subsection:1</span></span> | <span data-ttu-id="ae5ba-963">valueC</span><span class="sxs-lookup"><span data-stu-id="ae5ba-963">valueC</span></span> |
| <span data-ttu-id="ae5ba-964">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-964">json_array:subsection:2</span></span> | <span data-ttu-id="ae5ba-965">valueD</span><span class="sxs-lookup"><span data-stu-id="ae5ba-965">valueD</span></span> |

<span data-ttu-id="ae5ba-966">En la aplicación de ejemplo, la clase POCO siguiente está disponible para enlazar los pares clave-valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-966">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="ae5ba-967">Después del enlace, `JsonArrayExample.Key` contiene el valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-967">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="ae5ba-968">Los valores de la subsección se almacenan en la propiedad de la matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-968">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="ae5ba-969">Índice de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-969">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="ae5ba-970">Valor de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="ae5ba-970">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="ae5ba-971">0</span><span class="sxs-lookup"><span data-stu-id="ae5ba-971">0</span></span>                                   | <span data-ttu-id="ae5ba-972">valueB</span><span class="sxs-lookup"><span data-stu-id="ae5ba-972">valueB</span></span>                              |
| <span data-ttu-id="ae5ba-973">1</span><span class="sxs-lookup"><span data-stu-id="ae5ba-973">1</span></span>                                   | <span data-ttu-id="ae5ba-974">valueC</span><span class="sxs-lookup"><span data-stu-id="ae5ba-974">valueC</span></span>                              |
| <span data-ttu-id="ae5ba-975">2</span><span class="sxs-lookup"><span data-stu-id="ae5ba-975">2</span></span>                                   | <span data-ttu-id="ae5ba-976">valueD</span><span class="sxs-lookup"><span data-stu-id="ae5ba-976">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="ae5ba-977">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="ae5ba-977">Custom configuration provider</span></span>

<span data-ttu-id="ae5ba-978">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="ae5ba-978">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="ae5ba-979">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-979">The provider has the following characteristics:</span></span>

* <span data-ttu-id="ae5ba-980">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-980">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="ae5ba-981">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-981">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="ae5ba-982">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-982">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="ae5ba-983">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-983">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="ae5ba-984">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-984">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="ae5ba-985">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-985">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="ae5ba-986">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-986">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ae5ba-987">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-987">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ae5ba-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ae5ba-989">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-989">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ae5ba-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ae5ba-991">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-991">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ae5ba-992">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-992">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="ae5ba-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ae5ba-994">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-994">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ae5ba-995">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-995">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ae5ba-996">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-996">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="ae5ba-997">Acceso a la configuración durante el inicio</span><span class="sxs-lookup"><span data-stu-id="ae5ba-997">Access configuration during startup</span></span>

<span data-ttu-id="ae5ba-998">Inserte `IConfiguration` en el constructor `Startup` para acceder a los valores de configuración en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-998">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ae5ba-999">Para acceder a la configuración en `Startup.Configure`, inserte `IConfiguration` directamente en el método o use la instancia desde el constructor:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-999">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="ae5ba-1000">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1000">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="ae5ba-1001">Acceso a la configuración en una página de Razor Pages o en una vista de MVC</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1001">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="ae5ba-1002">Para acceder a los valores de configuración en una página de Razor Pages o una vista de MVC, agregue una [directiva using](xref:mvc/views/razor#using) ([Referencia de C#: directiva using](/dotnet/csharp/language-reference/keywords/using-directive)) para el [espacio de nombres Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inyecte <xref:Microsoft.Extensions.Configuration.IConfiguration> en la página o en la vista.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1002">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="ae5ba-1003">En una página de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1003">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="ae5ba-1004">En una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1004">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="ae5ba-1005">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1005">Add configuration from an external assembly</span></span>

<span data-ttu-id="ae5ba-1006">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1006">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ae5ba-1007">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1007">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae5ba-1008">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ae5ba-1008">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
