---
title: Configuración en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la API de configuración para una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 7565ede55acd936072fc1930918d46808548f287
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762352"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d2ebf-103">Configuración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2ebf-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d2ebf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2ebf-105">La configuración de ASP.NET Core se realiza mediante uno o varios [proveedores de configuración](#cp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="d2ebf-106">Los proveedores de configuración leen los datos sobre los ajustes de los pares clave-valor mediante distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="d2ebf-107">Archivos de configuración, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d2ebf-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="d2ebf-108">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-108">Environment variables</span></span>
* <span data-ttu-id="d2ebf-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-109">Azure Key Vault</span></span>
* <span data-ttu-id="d2ebf-110">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="d2ebf-110">Azure App Configuration</span></span>
* <span data-ttu-id="d2ebf-111">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-111">Command-line arguments</span></span>
* <span data-ttu-id="d2ebf-112">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="d2ebf-113">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="d2ebf-113">Directory files</span></span>
* <span data-ttu-id="d2ebf-114">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-114">In-memory .NET objects</span></span>

<span data-ttu-id="d2ebf-115">En este tema se proporciona información sobre la configuración de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="d2ebf-116">Para más información sobre el uso de la configuración en las aplicaciones de consola, consulte [Configuración de .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="d2ebf-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2ebf-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="d2ebf-118">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-118">Default configuration</span></span>

<span data-ttu-id="d2ebf-119">Las aplicaciones web de ASP.NET Core creadas con [dotnet new](/dotnet/core/tools/dotnet-new) o con Visual Studio generan el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="d2ebf-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="d2ebf-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  agrega un elemento `IConfiguration` existente como origen.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="d2ebf-122">En el caso de una configuración predeterminada, agrega la configuración del [host](#hvac) y lo establece como el primer origen para la configuración de la _aplicación_.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="d2ebf-123">[appsettings.json](#appsettingsjson) con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="d2ebf-124">*appsettings.* `Environment` *.json* con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="d2ebf-125">Por ejemplo, *appsettings*.***Production***.*json* y *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-125">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="d2ebf-126">[Secretos de la aplicación](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="d2ebf-127">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d2ebf-128">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="d2ebf-129">Los proveedores de configuración que se agregan posteriormente invalidan los ajustes de configuración de la clave anteriores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="d2ebf-130">Por ejemplo, si se establece `MyKey` tanto en *appsettings.json* como en el entorno, se usa el valor del entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="d2ebf-131">Con los proveedores de configuración predeterminados, el [proveedor de configuración de línea de comandos](#clcp) reemplaza al resto de proveedores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="d2ebf-132">Para obtener más información sobre `CreateDefaultBuilder`, consulte el artículo [Configuración predeterminada del generador](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="d2ebf-133">El código siguiente muestra los proveedores de configuración habilitados en el orden en el que se han agregado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="d2ebf-134">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d2ebf-134">appsettings.json</span></span>

<span data-ttu-id="d2ebf-135">Fíjese en el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-135">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d2ebf-136">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-136">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-137">El elemento <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> predeterminado carga la configuración en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-137">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="d2ebf-138">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d2ebf-138">*appsettings.json*</span></span>
1. <span data-ttu-id="d2ebf-139">*appsettings.* `Environment` *.json*: por ejemplo, los archivos *appsettings*.***Production***.*json* y *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-139">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="d2ebf-140">La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-140">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="d2ebf-141">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-141">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d2ebf-142">Los valores de *appsettings*.`Environment`.*json* invalidan las claves de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-142">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="d2ebf-143">Por ejemplo, de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-143">For example, by default:</span></span>

* <span data-ttu-id="d2ebf-144">En desarrollo, la configuración de *appsettings*.***Development***.*json* sobrescribe los valores de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-144">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="d2ebf-145">En producción, la configuración de *appsettings*.***Production***.*json* sobrescribe los valores de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-145">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="d2ebf-146">Por ejemplo, al implementar la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-146">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="d2ebf-147">Enlace de datos de configuración jerárquica mediante el patrón de opciones</span><span class="sxs-lookup"><span data-stu-id="d2ebf-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="d2ebf-148">Si usa la configuración [predeterminada](#default), los archivos *appsettings.json* y *appsettings.* `Environment` *.json* están habilitados con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="d2ebf-149">Los cambios realizados en los archivos *appsettings.json* y *appsettings.* `Environment` *.json* ***después*** de iniciar la aplicación los lee el [proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d2ebf-150">Consulte la sección [Proveedor de configuración JSON](#jcp) en este artículo para obtener información sobre cómo agregar archivos de configuración JSON adicionales.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="d2ebf-151">Combinación de colecciones de servicios</span><span class="sxs-lookup"><span data-stu-id="d2ebf-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="d2ebf-152">Administrador de seguridad y secretos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-152">Security and secret manager</span></span>

<span data-ttu-id="d2ebf-153">Directrices para los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="d2ebf-154">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d2ebf-155">Se puede usar el [administrador de secretos](xref:security/app-secrets) para almacenar secretos en entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-155">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="d2ebf-156">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d2ebf-157">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d2ebf-158">De forma [predeterminada](#default), el [administrador de secretos](xref:security/app-secrets) lee los ajustes de configuración después de los archivos *appsettings.json* y *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-158">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="d2ebf-159">Para obtener más información sobre cómo almacenar contraseñas u otros datos confidenciales consulte:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-159">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d2ebf-160"><xref:security/app-secrets>:  incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-160"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d2ebf-161">El administrador de secretos usa el [proveedor de configuración de archivo](#fcp) para almacenar secretos de usuario en un archivo JSON en el sistema local.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-161">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="d2ebf-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d2ebf-163">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-163">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="d2ebf-164">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-164">Environment variables</span></span>

<span data-ttu-id="d2ebf-165">Al usar la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de los pares clave-valor de la variable de entorno después de leer *appsettings.json*, *appsettings.* `Environment` *.json* y el [administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-165">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d2ebf-166">Por lo tanto, los valores de clave que se leen del entorno reemplazan los valores que se leen de *appsettings.json*, *appsettings.* `Environment` *.json* y del administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-166">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d2ebf-167">Los siguientes comandos `set`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-167">The following `set` commands:</span></span>

* <span data-ttu-id="d2ebf-168">Establecen las claves de entorno y los valores del [ejemplo anterior](#appsettingsjson) en Windows.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-168">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="d2ebf-169">Prueban la configuración al usar la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-169">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="d2ebf-170">El comando `dotnet run` debe ejecutarse en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-170">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="d2ebf-171">Los ajustes de configuración del entorno anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-171">The preceding environment settings:</span></span>

* <span data-ttu-id="d2ebf-172">Solo se establecen en procesos iniciados desde la ventana de comandos en la que se establecieron.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-172">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d2ebf-173">No los podrán leer los exploradores que se inician con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-173">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d2ebf-174">Los siguientes comandos [setx](/windows-server/administration/windows-commands/setx) se pueden usar para establecer las claves de entorno y los valores en Windows.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-174">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="d2ebf-175">A diferencia de `set`, la configuración de `setx` se conserva.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-175">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d2ebf-176">`/M` establece la variable en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-176">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="d2ebf-177">Si no se usa el modificador `/M`, se establece una variable de entorno de usuario.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-177">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="d2ebf-178">Para comprobar que los comandos anteriores invalidan *appsettings.json* y *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-178">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="d2ebf-179">Con Visual Studio: salga y reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-179">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="d2ebf-180">Con la CLI: abra una nueva ventana de comandos y escriba `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-180">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="d2ebf-181">Llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una cadena para especificar un prefijo para las variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-181">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="d2ebf-182">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-182">In the preceding code:</span></span>

* <span data-ttu-id="d2ebf-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d2ebf-184">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-184">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="d2ebf-185">Las variables de entorno establecidas con el prefijo `MyCustomPrefix_` invalidan los proveedores de configuración [predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-185">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="d2ebf-186">Esto incluye las variables de entorno sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-186">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="d2ebf-187">El prefijo se quita cuando se leen los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-187">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="d2ebf-188">Los siguientes comandos prueban el prefijo personalizado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-188">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="d2ebf-189">La [configuración predeterminada](#default) carga las variables de entorno y los argumentos de la línea de comandos con los prefijos `DOTNET_` y `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-189">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="d2ebf-190">ASP.NET Core usa los prefijos `DOTNET_` y `ASPNETCORE_` para la [configuración del host y la aplicación](xref:fundamentals/host/generic-host#host-configuration), pero no para la del usuario.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-190">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="d2ebf-191">Para obtener más información sobre la configuración del host y de la aplicación, consulte el artículo [Host genérico de .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-191">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="d2ebf-192">En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-192">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d2ebf-193">Los ajustes de configuración de Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-193">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d2ebf-194">Se cifran en reposo y se transmiten a través de un canal cifrado.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-194">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d2ebf-195">Se exponen como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-195">Exposed as environment variables.</span></span>

<span data-ttu-id="d2ebf-196">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-196">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d2ebf-197">Consulte la sección [Prefijos de cadena de conexión](#constr) para obtener información sobre las cadenas de conexión de base de datos de Azure.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-197">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="d2ebf-198">Variables de entorno configuradas en launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="d2ebf-198">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="d2ebf-199">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-199">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="d2ebf-200">Línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-200">Command-line</span></span>

<span data-ttu-id="d2ebf-201">Si se usa la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de los pares de clave-valor de argumento de la línea de comandos después de los siguientes orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-201">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="d2ebf-202">Archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-202">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d2ebf-203">[Secretos de aplicación (administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-203">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d2ebf-204">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-204">Environment variables.</span></span>

<span data-ttu-id="d2ebf-205">De [forma predeterminada](#default), los valores de configuración establecidos en la línea de comandos reemplazan los valores de configuración establecidos con el resto de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-205">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="d2ebf-206">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-206">Command-line arguments</span></span>

<span data-ttu-id="d2ebf-207">El comando siguiente establece las claves y los valores mediante `=`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-207">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="d2ebf-208">El comando siguiente establece las claves y los valores mediante `/`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-208">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="d2ebf-209">El comando siguiente establece las claves y los valores mediante `--`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-209">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="d2ebf-210">El valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-210">The key value:</span></span>

* <span data-ttu-id="d2ebf-211">Debe seguir a un signo `=`, o bien la clave debe tener un prefijo `--` o `/` cuando el valor sigue a un espacio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-211">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="d2ebf-212">No es necesario si se usa `=`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-212">Isn't required if `=` is used.</span></span> <span data-ttu-id="d2ebf-213">Por ejemplo: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-213">For example, `MySetting=`.</span></span>

<span data-ttu-id="d2ebf-214">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan `=` con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-214">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="d2ebf-215">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="d2ebf-215">Switch mappings</span></span>

<span data-ttu-id="d2ebf-216">Las asignaciones de modificador admiten la lógica de sustitución de nombres de **clave**.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-216">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="d2ebf-217">Proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-217">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d2ebf-218">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-218">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d2ebf-219">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-219">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d2ebf-220">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-220">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d2ebf-221">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-221">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d2ebf-222">Los modificadores deben comenzar con `-` o `--`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-222">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="d2ebf-223">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-223">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d2ebf-224">Para usar un diccionario de asignaciones de modificador, páselo a la llamada a `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-224">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="d2ebf-225">El código siguiente muestra los valores de clave para las claves reemplazadas:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-225">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-226">El siguiente comando sirve para probar el reemplazo de claves:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-226">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d2ebf-227">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-227">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d2ebf-228">En la llamada de `AddCommandLine` al método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-228">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d2ebf-229">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-229">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d2ebf-230">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="d2ebf-230">Hierarchical configuration data</span></span>

<span data-ttu-id="d2ebf-231">La API de configuración lee los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-231">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d2ebf-232">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-232">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d2ebf-233">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-233">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-234">La mejor manera de leer datos de configuración jerárquica es usar el patrón de opciones.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-234">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="d2ebf-235">Para obtener más información, vea la sección [Enlace de datos de configuración jerárquica](#optpat) en este documento.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-235">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="d2ebf-236">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d2ebf-237">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-237">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="d2ebf-238">Claves y valores de configuración</span><span class="sxs-lookup"><span data-stu-id="d2ebf-238">Configuration keys and values</span></span>

<span data-ttu-id="d2ebf-239">Las claves de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-239">Configuration keys:</span></span>

* <span data-ttu-id="d2ebf-240">No distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-240">Are case-insensitive.</span></span> <span data-ttu-id="d2ebf-241">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-241">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d2ebf-242">Si se establece una clave y un valor en más de un proveedor de configuración, se usa el valor del último proveedor agregado.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-242">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="d2ebf-243">Para obtener más información, vea la sección [Configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-243">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="d2ebf-244">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="d2ebf-244">Hierarchical keys</span></span>
  * <span data-ttu-id="d2ebf-245">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-245">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d2ebf-246">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-246">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d2ebf-247">Todas las plataformas admiten un carácter de subrayado doble, `__`, que se convierte automáticamente en un signo de dos puntos `:`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-247">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="d2ebf-248">En Azure Key Vault, las claves jerárquicas usan `--` como separador.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-248">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="d2ebf-249">El [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) reemplaza automáticamente `--` con `:` cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-249">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d2ebf-250"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-250">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d2ebf-251">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#boa).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-251">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="d2ebf-252">Los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-252">Configuration values:</span></span>

* <span data-ttu-id="d2ebf-253">Son cadenas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-253">Are strings.</span></span>
* <span data-ttu-id="d2ebf-254">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-254">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="d2ebf-255">Proveedores de configuración</span><span class="sxs-lookup"><span data-stu-id="d2ebf-255">Configuration providers</span></span>

<span data-ttu-id="d2ebf-256">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-256">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d2ebf-257">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-257">Provider</span></span> | <span data-ttu-id="d2ebf-258">Proporciona la configuración de</span><span class="sxs-lookup"><span data-stu-id="d2ebf-258">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="d2ebf-259">Proveedor de configuración de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-259">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="d2ebf-260">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-260">Azure Key Vault</span></span> |
| [<span data-ttu-id="d2ebf-261">Proveedor de configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="d2ebf-261">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="d2ebf-262">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="d2ebf-262">Azure App Configuration</span></span> |
| [<span data-ttu-id="d2ebf-263">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-263">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="d2ebf-264">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-264">Command-line parameters</span></span> |
| [<span data-ttu-id="d2ebf-265">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-265">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d2ebf-266">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="d2ebf-266">Custom source</span></span> |
| [<span data-ttu-id="d2ebf-267">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-267">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="d2ebf-268">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-268">Environment variables</span></span> |
| [<span data-ttu-id="d2ebf-269">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-269">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d2ebf-270">Archivos INI, JSON y XML</span><span class="sxs-lookup"><span data-stu-id="d2ebf-270">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="d2ebf-271">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-271">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d2ebf-272">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="d2ebf-272">Directory files</span></span> |
| [<span data-ttu-id="d2ebf-273">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-273">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d2ebf-274">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-274">In-memory collections</span></span> |
| [<span data-ttu-id="d2ebf-275">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-275">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="d2ebf-276">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="d2ebf-276">File in the user profile directory</span></span> |

<span data-ttu-id="d2ebf-277">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-277">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="d2ebf-278">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-278">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d2ebf-279">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-279">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d2ebf-280">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d2ebf-280">*appsettings.json*</span></span>
1. <span data-ttu-id="d2ebf-281">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="d2ebf-281">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="d2ebf-282">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-282">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="d2ebf-283">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-283">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d2ebf-284">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-284">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="d2ebf-285">Una práctica común es agregar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-285">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d2ebf-286">La secuencia de proveedores anterior se usa en la [configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-286">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="d2ebf-287">Prefijos de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="d2ebf-287">Connection string prefixes</span></span>

<span data-ttu-id="d2ebf-288">La API de configuración tiene reglas de procesamiento especiales para cuatro variables de entorno de cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-288">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="d2ebf-289">Estas cadenas de conexión están implicadas en la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-289">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d2ebf-290">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación con la [configuración predeterminada](#default) o cuando no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-290">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d2ebf-291">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="d2ebf-291">Connection string prefix</span></span> | <span data-ttu-id="d2ebf-292">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-292">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d2ebf-293">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="d2ebf-293">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d2ebf-294">MySQL</span><span class="sxs-lookup"><span data-stu-id="d2ebf-294">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d2ebf-295">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="d2ebf-295">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d2ebf-296">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d2ebf-296">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d2ebf-297">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-297">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d2ebf-298">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-298">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d2ebf-299">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-299">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d2ebf-300">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-300">Environment variable key</span></span> | <span data-ttu-id="d2ebf-301">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="d2ebf-301">Converted configuration key</span></span> | <span data-ttu-id="d2ebf-302">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-302">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-303">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-303">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-304">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-305">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-305">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-306">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-307">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-307">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-308">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-309">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-309">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="d2ebf-310">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-310">File configuration provider</span></span>

<span data-ttu-id="d2ebf-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d2ebf-312">Los siguientes proveedores de configuración se derivan de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-312">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="d2ebf-313">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="d2ebf-313">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d2ebf-314">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="d2ebf-314">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="d2ebf-315">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="d2ebf-315">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d2ebf-316">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="d2ebf-316">INI configuration provider</span></span>

<span data-ttu-id="d2ebf-317"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-317">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-318">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-318">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="d2ebf-319">En el código anterior, se reemplaza la configuración de los archivos *MyIniConfig.ini* y *MyIniConfig*.`Environment`.*ini* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-319">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d2ebf-320">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-320">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d2ebf-321">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-321">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d2ebf-322">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-322">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="d2ebf-323">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-323">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="d2ebf-324">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="d2ebf-324">JSON configuration provider</span></span>

<span data-ttu-id="d2ebf-325"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-325">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="d2ebf-326">Las sobrecargas pueden especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-326">Overloads can specify:</span></span>

* <span data-ttu-id="d2ebf-327">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-327">Whether the file is optional.</span></span>
* <span data-ttu-id="d2ebf-328">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-328">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="d2ebf-329">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-329">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="d2ebf-330">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-330">The preceding code:</span></span>

* <span data-ttu-id="d2ebf-331">Configura el proveedor de configuración JSON para que cargue el archivo *MyConfig.json* con las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-331">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="d2ebf-332">`optional: true`: el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-332">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="d2ebf-333">`reloadOnChange: true`: el archivo se recarga cuando se guardan los cambios.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-333">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="d2ebf-334">Lee los [proveedores de configuración predeterminados](#default) antes que el archivo *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-334">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="d2ebf-335">La configuración del archivo *MyConfig.json* invalida la de los proveedores de configuración predeterminados, incluidos el [proveedor de configuración de variables de entorno](#evcp) y el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-335">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d2ebf-336">Normalmente ***no*** querrá que un archivo JSON personalizado invalide los valores establecidos en el [proveedor de configuración de variables de entorno](#evcp) ni en el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-336">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d2ebf-337">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-337">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="d2ebf-338">En el código anterior, la configuración de los archivos *MyConfig.json* y *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-338">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="d2ebf-339">Invalida la configuración de los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-339">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d2ebf-340">Es reemplazada por la configuración del [proveedor de configuración de variables de entorno](#evcp) y del [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-340">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d2ebf-341">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-341">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="d2ebf-342">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-342">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="d2ebf-343">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="d2ebf-343">XML configuration provider</span></span>

<span data-ttu-id="d2ebf-344"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-344">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-345">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-345">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="d2ebf-346">En el código anterior, se reemplaza la configuración de los archivos *MyXMLFile.xml* y *MyXMLFile*.`Environment`.*xml* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-346">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d2ebf-347">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-347">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d2ebf-348">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-348">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d2ebf-349">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-349">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="d2ebf-350">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-350">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-351">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-351">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="d2ebf-352">El código siguiente lee el archivo de configuración anterior y muestra las claves y los valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-352">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-353">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-353">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d2ebf-354">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-354">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d2ebf-355">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d2ebf-355">key:attribute</span></span>
* <span data-ttu-id="d2ebf-356">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d2ebf-356">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d2ebf-357">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-357">Key-per-file configuration provider</span></span>

<span data-ttu-id="d2ebf-358"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-358">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d2ebf-359">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-359">The key is the file name.</span></span> <span data-ttu-id="d2ebf-360">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-360">The value contains the file's contents.</span></span> <span data-ttu-id="d2ebf-361">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-361">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d2ebf-362">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-362">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d2ebf-363">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-363">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d2ebf-364">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-364">Overloads permit specifying:</span></span>

* <span data-ttu-id="d2ebf-365">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-365">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d2ebf-366">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-366">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d2ebf-367">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-367">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d2ebf-368">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-368">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d2ebf-369">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-369">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="d2ebf-370">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-370">Memory configuration provider</span></span>

<span data-ttu-id="d2ebf-371"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-371">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d2ebf-372">El código siguiente agrega una colección en memoria al sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-372">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="d2ebf-373">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra las opciones de configuración anteriores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-373">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-374">En el código anterior, `config.AddInMemoryCollection(Dict)` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-374">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d2ebf-375">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-375">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d2ebf-376">Consulte la sección [Enlace de matrices](#boa) para ver otro ejemplo que usa `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-376">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="d2ebf-377">GetValue</span><span class="sxs-lookup"><span data-stu-id="d2ebf-377">GetValue</span></span>

<span data-ttu-id="d2ebf-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-379">En el código anterior, si `NumberKey` no se encuentra en la configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-379">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d2ebf-380">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="d2ebf-380">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d2ebf-381">Para los ejemplos que aparecen a continuación, considere el siguiente archivo *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-381">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="d2ebf-382">El código siguiente agrega *MySubsection.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-382">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="d2ebf-383">GetSection</span><span class="sxs-lookup"><span data-stu-id="d2ebf-383">GetSection</span></span>

<span data-ttu-id="d2ebf-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) devuelve una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d2ebf-385">El código siguiente devuelve los valores de `section1`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-385">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-386">El código siguiente devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-386">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-387">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-387">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d2ebf-388">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-388">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d2ebf-389">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-389">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d2ebf-390">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-390">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="d2ebf-391">GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="d2ebf-391">GetChildren and Exists</span></span>

<span data-ttu-id="d2ebf-392">El código siguiente llama a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) y devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-392">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-393">El código anterior llama a [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para comprobar que la sección existe:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-393">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="d2ebf-394">Enlace de matrices</span><span class="sxs-lookup"><span data-stu-id="d2ebf-394">Bind an array</span></span>

<span data-ttu-id="d2ebf-395">El método [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) permite enlazar matrices a objetos mediante el uso de los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-395">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d2ebf-396">Cualquier formato de matriz que exponga un segmento de clave numérica es capaz de enlazar una matriz a una matriz de clase [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-396">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="d2ebf-397">Tenga en cuenta este archivo *MyArray.json* de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="d2ebf-397">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="d2ebf-398">El código siguiente agrega *MyArray.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-398">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="d2ebf-399">El código siguiente lee la configuración y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-399">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-400">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-400">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="d2ebf-401">En el resultado anterior, el índice 3 tiene el valor `value40`, que corresponde a `"4": "value40",` en *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-401">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="d2ebf-402">Los índices de matriz enlazados son continuos y no están enlazados al índice de la clave de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-402">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="d2ebf-403">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en objetos enlazados.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-403">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="d2ebf-404">El código siguiente carga la configuración de `array:entries` con el método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-404">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="d2ebf-405">El código siguiente lee la configuración de `arrayDict` `Dictionary` y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-405">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-406">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-406">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="d2ebf-407">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-407">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d2ebf-408">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-408">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d2ebf-409">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-409">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d2ebf-410">Cualquier proveedor de configuración que lea el par clave-valor del índice &num;3 puede proporcionar el elemento de configuración omitido para el índice &num;3 antes del enlace a la instancia `ArrayExample`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-410">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="d2ebf-411">Fíjese en el siguiente archivo *Value3.json* de la descarga de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-411">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="d2ebf-412">En el código siguiente se incluye la configuración para *Value3.json* y `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-412">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="d2ebf-413">El código siguiente lee la configuración anterior y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-413">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-414">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-414">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="d2ebf-415">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-415">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d2ebf-416">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-416">Custom configuration provider</span></span>

<span data-ttu-id="d2ebf-417">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-417">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d2ebf-418">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-418">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d2ebf-419">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-419">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d2ebf-420">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-420">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d2ebf-421">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-421">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d2ebf-422">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-422">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d2ebf-423">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-423">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d2ebf-424">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-424">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d2ebf-425">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-425">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d2ebf-426">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-426">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d2ebf-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d2ebf-428">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-428">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d2ebf-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d2ebf-430">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-430">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d2ebf-431">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-431">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="d2ebf-432">Puesto que las [claves de configuración no distinguen entre mayúsculas y minúsculas](#keys), el diccionario empleado para iniciar la base de datos se crea con el comparador que tampoco hace tal distinción ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-432">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="d2ebf-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d2ebf-434">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-434">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d2ebf-435">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-435">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d2ebf-436">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-436">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="d2ebf-437">Acceso a la configuración en métodos Startup</span><span class="sxs-lookup"><span data-stu-id="d2ebf-437">Access configuration in Startup</span></span>

<span data-ttu-id="d2ebf-438">En el código siguiente se muestran los datos de configuración de los métodos `Startup`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-438">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="d2ebf-439">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-439">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="d2ebf-440">Acceso a la configuración en Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d2ebf-440">Access configuration in Razor Pages</span></span>

<span data-ttu-id="d2ebf-441">En el código siguiente se muestran los datos de configuración en una página de RazorPages:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-441">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="d2ebf-442">En el siguiente código se agrega `MyOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-442">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d2ebf-443">En el marcado siguiente se usa la directiva [`@inject`](xref:mvc/views/razor#inject) Razor para resolver y mostrar los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-443">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="d2ebf-444">Acceso a la configuración en un archivo de vista de MVC</span><span class="sxs-lookup"><span data-stu-id="d2ebf-444">Access configuration in a MVC view file</span></span>

<span data-ttu-id="d2ebf-445">En el código siguiente se muestran los datos de configuración de una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-445">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="d2ebf-446">Configurar opciones con un delegado</span><span class="sxs-lookup"><span data-stu-id="d2ebf-446">Configure options with a delegate</span></span>

<span data-ttu-id="d2ebf-447">Las opciones configuradas en un delegado invalidan los valores establecidos en los proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-447">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="d2ebf-448">La configuración de opciones con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-448">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d2ebf-449">En el código siguiente, se agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-449">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d2ebf-450">Usa un delegado para configurar los valores de `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-450">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="d2ebf-451">En el código siguiente se muestran los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-451">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="d2ebf-452">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appSettings.json* y, luego, se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-452">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d2ebf-453">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="d2ebf-453">Host versus app configuration</span></span>

<span data-ttu-id="d2ebf-454">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-454">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d2ebf-455">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-455">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d2ebf-456">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-456">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d2ebf-457">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-457">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d2ebf-458">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-458">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="d2ebf-459">Configuración predeterminada del host</span><span class="sxs-lookup"><span data-stu-id="d2ebf-459">Default host configuration</span></span>

<span data-ttu-id="d2ebf-460">Para obtener más información sobre la configuración predeterminada al usar el [host de web](xref:fundamentals/host/web-host), vea la [versión de este tema para ASP.NET Core 2.2](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-460">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="d2ebf-461">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-461">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d2ebf-462">Las variables de entorno con el prefijo `DOTNET_` (por ejemplo, `DOTNET_ENVIRONMENT`) mediante el [proveedor de configuración de variables de entorno](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-462">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="d2ebf-463">El prefijo (`DOTNET_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-463">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d2ebf-464">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-464">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d2ebf-465">La configuración predeterminada del host de web se ha establecido (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="d2ebf-465">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="d2ebf-466">Kestrel se usa como el servidor web y se ha configurado mediante los proveedores de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-466">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="d2ebf-467">Agregar el middleware de filtrado de host</span><span class="sxs-lookup"><span data-stu-id="d2ebf-467">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="d2ebf-468">Agregue el middleware de encabezados reenviados si la variable de entorno `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-468">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="d2ebf-469">Habilite la integración con IIS.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-469">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d2ebf-470">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="d2ebf-470">Other configuration</span></span>

<span data-ttu-id="d2ebf-471">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-471">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d2ebf-472">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-472">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d2ebf-473">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-473">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d2ebf-474">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-474">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d2ebf-475">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-475">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d2ebf-476">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-476">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d2ebf-477">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-477">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="d2ebf-478">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-478">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d2ebf-479">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-479">Add configuration from an external assembly</span></span>

<span data-ttu-id="d2ebf-480">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-480">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d2ebf-481">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-481">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d2ebf-482">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d2ebf-482">Additional resources</span></span>

* [<span data-ttu-id="d2ebf-483">Configuración del código fuente</span><span class="sxs-lookup"><span data-stu-id="d2ebf-483">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d2ebf-484">La configuración de la aplicación en ASP.NET Core se basa en pares clave-valor establecidos por *proveedores de configuración*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-484">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="d2ebf-485">Los proveedores de configuración leen los datos de configuración en los pares clave-valor de distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-485">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="d2ebf-486">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-486">Azure Key Vault</span></span>
* <span data-ttu-id="d2ebf-487">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="d2ebf-487">Azure App Configuration</span></span>
* <span data-ttu-id="d2ebf-488">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-488">Command-line arguments</span></span>
* <span data-ttu-id="d2ebf-489">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-489">Custom providers (installed or created)</span></span>
* <span data-ttu-id="d2ebf-490">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="d2ebf-490">Directory files</span></span>
* <span data-ttu-id="d2ebf-491">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-491">Environment variables</span></span>
* <span data-ttu-id="d2ebf-492">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-492">In-memory .NET objects</span></span>
* <span data-ttu-id="d2ebf-493">Archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="d2ebf-493">Settings files</span></span>

<span data-ttu-id="d2ebf-494">Los paquetes de configuración para escenarios de proveedor de configuración común ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) se incluyen en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-494">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d2ebf-495">Los ejemplos de código que siguen y en la aplicación de ejemplo utilizan el espacio de nombres <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-495">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="d2ebf-496">El *patrón de opciones* es una extensión de los conceptos de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-496">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="d2ebf-497">Las opciones usan clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-497">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="d2ebf-498">Para obtener más información, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-498">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d2ebf-499">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2ebf-499">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d2ebf-500">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="d2ebf-500">Host versus app configuration</span></span>

<span data-ttu-id="d2ebf-501">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-501">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d2ebf-502">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-502">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d2ebf-503">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-503">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d2ebf-504">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-504">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d2ebf-505">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-505">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d2ebf-506">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="d2ebf-506">Other configuration</span></span>

<span data-ttu-id="d2ebf-507">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-507">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d2ebf-508">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-508">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d2ebf-509">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-509">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d2ebf-510">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-510">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d2ebf-511">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-511">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d2ebf-512">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-512">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d2ebf-513">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-513">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="d2ebf-514">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-514">Default configuration</span></span>

<span data-ttu-id="d2ebf-515">Las aplicaciones web basadas en las plantillas de [dotnet new](/dotnet/core/tools/dotnet-new) de ASP.NET Core llaman a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> al crear un host.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-515">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="d2ebf-516">`CreateDefaultBuilder` proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-516">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="d2ebf-517">El contenido siguiente es válido para las aplicaciones que usen el [host de web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-517">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d2ebf-518">Para obtener más información sobre la configuración predeterminada al usar el [host genérico](xref:fundamentals/host/generic-host), vea la [versión más reciente de ese tema](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-518">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="d2ebf-519">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-519">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d2ebf-520">Variables de entorno prefijadas con `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`) con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-520">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d2ebf-521">El prefijo (`ASPNETCORE_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-521">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d2ebf-522">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-522">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d2ebf-523">La configuración de la aplicación la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-523">App configuration is provided from:</span></span>
  * <span data-ttu-id="d2ebf-524">*appsettings.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-524">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d2ebf-525">*appsettings.{Entorno}.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-525">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d2ebf-526">[Administrador de secretos](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-526">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d2ebf-527">Variables de entorno con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-527">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="d2ebf-528">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-528">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="d2ebf-529">Seguridad</span><span class="sxs-lookup"><span data-stu-id="d2ebf-529">Security</span></span>

<span data-ttu-id="d2ebf-530">Adopte los procedimientos siguientes para proteger los datos de configuración confidenciales:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-530">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="d2ebf-531">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-531">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="d2ebf-532">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-532">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d2ebf-533">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-533">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d2ebf-534">Para obtener más información, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-534">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d2ebf-535"><xref:security/app-secrets>: incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-535"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d2ebf-536">El Administrador de secretos usa el proveedor de configuración de archivo para almacenar secretos de usuario en un archivo JSON del sistema local.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-536">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="d2ebf-537">El proveedor de configuración de archivo se describe más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-537">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="d2ebf-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d2ebf-539">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-539">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d2ebf-540">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="d2ebf-540">Hierarchical configuration data</span></span>

<span data-ttu-id="d2ebf-541">La API de configuración es capaz de mantener los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-541">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d2ebf-542">En el siguiente archivo JSON, hay cuatro claves en una estructura jerárquica de dos secciones:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-542">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="d2ebf-543">Cuando el archivo se lee en la configuración, se crean claves únicas para mantener la estructura de datos jerárquicos original del origen de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-543">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="d2ebf-544">Las secciones y claves se disminuyen con el uso de dos puntos (`:`) para mantener la estructura original:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-544">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="d2ebf-545">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-545">section0:key0</span></span>
* <span data-ttu-id="d2ebf-546">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-546">section0:key1</span></span>
* <span data-ttu-id="d2ebf-547">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-547">section1:key0</span></span>
* <span data-ttu-id="d2ebf-548">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-548">section1:key1</span></span>

<span data-ttu-id="d2ebf-549">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d2ebf-550">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-550">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="d2ebf-551">Convenciones</span><span class="sxs-lookup"><span data-stu-id="d2ebf-551">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="d2ebf-552">Orígenes y proveedores</span><span class="sxs-lookup"><span data-stu-id="d2ebf-552">Sources and providers</span></span>

<span data-ttu-id="d2ebf-553">Al iniciar la aplicación, los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-553">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="d2ebf-554">Los proveedores de configuración que implementan la detección de cambios tienen la capacidad de volver a cargar la configuración cuando se cambia una configuración subyacente.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-554">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="d2ebf-555">Por ejemplo, el proveedor de configuración de archivos (descrito más adelante en este tema) y el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) implementan la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-555">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="d2ebf-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponible en el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d2ebf-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> se puede insertar en Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obtener la configuración de la clase.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="d2ebf-558">En los ejemplos siguientes, se usa el campo `_config` para tener acceso a los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-558">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="d2ebf-559">Los proveedores de configuración no pueden usar la inserción de dependencias, porque no está disponible cuando el host los configura.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-559">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="d2ebf-560">Teclas</span><span class="sxs-lookup"><span data-stu-id="d2ebf-560">Keys</span></span>

<span data-ttu-id="d2ebf-561">Las claves de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-561">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="d2ebf-562">Las claves no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-562">Keys are case-insensitive.</span></span> <span data-ttu-id="d2ebf-563">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-563">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d2ebf-564">Si los mismos o distintos proveedores de configuración establecen un valor para la misma clave, el último valor establecido en la clave es el valor que se usa.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-564">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="d2ebf-565">Para más información sobre las claves JSON duplicadas, vea [este problema de GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-565">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="d2ebf-566">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="d2ebf-566">Hierarchical keys</span></span>
  * <span data-ttu-id="d2ebf-567">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-567">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d2ebf-568">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-568">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d2ebf-569">Todas las plataformas admiten un carácter de subrayado doble (`__`), que se convierte automáticamente en un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-569">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="d2ebf-570">En Azure Key Vault, las claves jerárquicas usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-570">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d2ebf-571">Escriba código para reemplazar los guiones por dos puntos cuando los secretos se carguen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-571">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d2ebf-572"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-572">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d2ebf-573">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-573">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="d2ebf-574">Valores</span><span class="sxs-lookup"><span data-stu-id="d2ebf-574">Values</span></span>

<span data-ttu-id="d2ebf-575">Los valores de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-575">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="d2ebf-576">Los valores son cadenas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-576">Values are strings.</span></span>
* <span data-ttu-id="d2ebf-577">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-577">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="d2ebf-578">Proveedores</span><span class="sxs-lookup"><span data-stu-id="d2ebf-578">Providers</span></span>

<span data-ttu-id="d2ebf-579">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-579">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d2ebf-580">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-580">Provider</span></span> | <span data-ttu-id="d2ebf-581">Proporciona la configuración de&hellip;</span><span class="sxs-lookup"><span data-stu-id="d2ebf-581">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="d2ebf-582">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-582">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="d2ebf-583">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-583">Azure Key Vault</span></span> |
| <span data-ttu-id="d2ebf-584">[Proveedor de Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-584">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="d2ebf-585">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="d2ebf-585">Azure App Configuration</span></span> |
| [<span data-ttu-id="d2ebf-586">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-586">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="d2ebf-587">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-587">Command-line parameters</span></span> |
| [<span data-ttu-id="d2ebf-588">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-588">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d2ebf-589">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="d2ebf-589">Custom source</span></span> |
| [<span data-ttu-id="d2ebf-590">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-590">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="d2ebf-591">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-591">Environment variables</span></span> |
| [<span data-ttu-id="d2ebf-592">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-592">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d2ebf-593">Archivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-593">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="d2ebf-594">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-594">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d2ebf-595">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="d2ebf-595">Directory files</span></span> |
| [<span data-ttu-id="d2ebf-596">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-596">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d2ebf-597">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-597">In-memory collections</span></span> |
| <span data-ttu-id="d2ebf-598">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-598">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="d2ebf-599">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="d2ebf-599">File in the user profile directory</span></span> |

<span data-ttu-id="d2ebf-600">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-600">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="d2ebf-601">En este tema, los proveedores de configuración se describen en orden alfabético y no en el orden en el que el código los organiza.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-601">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="d2ebf-602">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-602">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d2ebf-603">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-603">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d2ebf-604">Archivos (*appsettings.json*, *appsettings.{Environment}.json*, donde `{Environment}` es el entorno de hospedaje actual de la aplicación)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-604">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="d2ebf-605">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d2ebf-605">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="d2ebf-606">[Secretos de usuario (administrador de secretos)](xref:security/app-secrets) (solo para entornos de desarrollo)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-606">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="d2ebf-607">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-607">Environment variables</span></span>
1. <span data-ttu-id="d2ebf-608">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-608">Command-line arguments</span></span>

<span data-ttu-id="d2ebf-609">Una práctica común es colocar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-609">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d2ebf-610">La secuencia de proveedores anterior se usa cuando se inicializa un nuevo generador de host con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-610">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d2ebf-611">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-611">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="d2ebf-612">Configurar el generador de host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="d2ebf-612">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="d2ebf-613">Para configurar el generador de host, realice una llamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> en el generador de host con la configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-613">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="d2ebf-614">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d2ebf-614">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d2ebf-615">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar los proveedores de configuración de la aplicación además de aquellos que `CreateDefaultBuilder` agrega automáticamente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-615">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="d2ebf-616">Reemplazar la configuración anterior con argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-616">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="d2ebf-617">Para proporcionar configuración de aplicaciones que se pueda reemplazar por argumentos de la línea de comandos, llame a `AddCommandLine` en último lugar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-617">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="d2ebf-618">Quitar proveedores agregados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="d2ebf-618">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="d2ebf-619">Para quitar los proveedores agregados por `CreateDefaultBuilder`, llame primero a [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) en [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="d2ebf-619">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="d2ebf-620">Usar la configuración durante el inicio de la aplicación</span><span class="sxs-lookup"><span data-stu-id="d2ebf-620">Consume configuration during app startup</span></span>

<span data-ttu-id="d2ebf-621">La configuración proporcionada a la aplicación en `ConfigureAppConfiguration` está disponible durante el inicio de la aplicación, incluido `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-621">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d2ebf-622">Para obtener más información, vea la sección [Acceso a la configuración durante el inicio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-622">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="d2ebf-623">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-623">Command-line Configuration Provider</span></span>

<span data-ttu-id="d2ebf-624"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de pares clave-valor de argumento de la línea de comandos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-624">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-625">Para activar la configuración de línea de comandos, se llama al método de extensión <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-625">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d2ebf-626">`AddCommandLine` se llama automáticamente al llamar a `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-626">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="d2ebf-627">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-627">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d2ebf-628">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-628">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d2ebf-629">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-629">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d2ebf-630">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-630">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d2ebf-631">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-631">Environment variables.</span></span>

<span data-ttu-id="d2ebf-632">`CreateDefaultBuilder` agrega el proveedor de configuración de línea de comandos al final.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-632">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="d2ebf-633">Los argumentos de la línea de comandos que se pasan en tiempo de ejecución invalidan la configuración establecida por los otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-633">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="d2ebf-634">`CreateDefaultBuilder` actúa cuando se construye el host.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-634">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="d2ebf-635">Por tanto, la configuración de línea de comandos activada por `CreateDefaultBuilder` puede afectar la manera en que se configura el host.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-635">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="d2ebf-636">Para las aplicaciones basadas en plantillas de ASP.NET Core, `CreateDefaultBuilder` ya realiza una llamada a `AddCommandLine`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-636">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d2ebf-637">Para agregar proveedores de configuración adicionales y mantener la capacidad de reemplazar la configuración de sus proveedores con argumentos de la línea de comandos, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddCommandLine` en último lugar.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-637">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="d2ebf-638">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-638">**Example**</span></span>

<span data-ttu-id="d2ebf-639">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-639">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="d2ebf-640">Abra un símbolo del sistema en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-640">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="d2ebf-641">Proporcione un argumento de la línea de comandos para el comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-641">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="d2ebf-642">Una vez que se ejecuta la aplicación, abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-642">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d2ebf-643">Observe que la salida contiene el par clave-valor para el argumento de línea de comandos de configuración proporcionado a `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-643">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="d2ebf-644">Argumentos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-644">Arguments</span></span>

<span data-ttu-id="d2ebf-645">El valor debe seguir a un signo igual (`=`) o la clave debe tener un prefijo (`--` o `/`) cuando el valor siga a un espacio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-645">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="d2ebf-646">El valor no es obligatorio si se usa un signo igual (por ejemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-646">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="d2ebf-647">Prefijo de la clave</span><span class="sxs-lookup"><span data-stu-id="d2ebf-647">Key prefix</span></span>               | <span data-ttu-id="d2ebf-648">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-648">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="d2ebf-649">Sin prefijo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-649">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="d2ebf-650">Dos guiones (`--`)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-650">Two dashes (`--`)</span></span>        | <span data-ttu-id="d2ebf-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="d2ebf-652">Barra diagonal (`/`)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-652">Forward slash (`/`)</span></span>      | <span data-ttu-id="d2ebf-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="d2ebf-654">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan un signo igual con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-654">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="d2ebf-655">Comandos de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-655">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="d2ebf-656">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="d2ebf-656">Switch mappings</span></span>

<span data-ttu-id="d2ebf-657">Las asignaciones de modificador admiten la lógica de sustitución de nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-657">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="d2ebf-658">Al crear manualmente la configuración con <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-658">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d2ebf-659">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-659">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d2ebf-660">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario (el reemplazo de la clave) para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-660">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d2ebf-661">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-661">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d2ebf-662">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-662">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d2ebf-663">Los modificadores deben empezar por un guion (`-`) o guion doble (`--`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-663">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="d2ebf-664">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-664">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d2ebf-665">Cree un diccionario de asignaciones de modificador.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-665">Create a switch mappings dictionary.</span></span> <span data-ttu-id="d2ebf-666">En el ejemplo siguiente, se crean dos asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-666">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="d2ebf-667">Al compilar el host, llame a `AddCommandLine` con el diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-667">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="d2ebf-668">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-668">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d2ebf-669">En la llamada de `AddCommandLine` del método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-669">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d2ebf-670">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino que permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-670">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="d2ebf-671">Después de crear el diccionario de asignaciones de modificador, contiene los datos que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-671">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="d2ebf-672">Clave</span><span class="sxs-lookup"><span data-stu-id="d2ebf-672">Key</span></span>       | <span data-ttu-id="d2ebf-673">Valor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-673">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="d2ebf-674">Si las claves de asignación de conmutador se usan al iniciar la aplicación, la configuración recibe el valor de configuración en la clave que el diccionario suministra:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-674">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="d2ebf-675">Una vez que se ejecuta el comando anterior, la configuración contiene los valores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-675">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="d2ebf-676">Key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-676">Key</span></span>               | <span data-ttu-id="d2ebf-677">Valor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-677">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="d2ebf-678">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-678">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="d2ebf-679"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de pares clave-valor de variables de entorno en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-679">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-680">Para activar la configuración de variables de entorno, llame al método de extensión <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-680">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d2ebf-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) permite establecer variables de entorno en Azure Portal que pueden invalidar la configuración de la aplicación mediante el proveedor de configuración de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="d2ebf-682">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-682">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d2ebf-683">`AddEnvironmentVariables` se usa para cargar variables de entorno con el prefijo `ASPNETCORE_` para la [configuración de host](#host-versus-app-configuration) al inicializar un nuevo generador de host con el [host de web](xref:fundamentals/host/web-host) y al llamar a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-683">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="d2ebf-684">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-684">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d2ebf-685">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-685">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d2ebf-686">Configuración de la aplicación desde variables de entorno sin prefijo mediante la llamada a `AddEnvironmentVariables` sin prefijo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-686">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="d2ebf-687">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-687">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d2ebf-688">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-688">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d2ebf-689">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-689">Command-line arguments.</span></span>

<span data-ttu-id="d2ebf-690">El proveedor de configuración de variables de entorno se llama una vez establecida la configuración desde los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-690">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="d2ebf-691">Llamar al proveedor en esta posición permite que la lectura de las variables de entorno en tiempo de ejecución invaliden la configuración establecida por los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-691">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="d2ebf-692">Para proporcionar la configuración de la aplicación desde variables de entorno adicionales, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddEnvironmentVariables` con el prefijo:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-692">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="d2ebf-693">Llame a `AddEnvironmentVariables` en último lugar para permitir que las variables de entorno con el prefijo especificado invaliden los valores de otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-693">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="d2ebf-694">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-694">**Example**</span></span>

<span data-ttu-id="d2ebf-695">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-695">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="d2ebf-696">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-696">Run the sample app.</span></span> <span data-ttu-id="d2ebf-697">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-697">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d2ebf-698">Observe que el resultado contiene el par clave y valor para la variable de entorno `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-698">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="d2ebf-699">El valor refleja el entorno en el que se ejecuta la aplicación, habitualmente `Development` cuando se ejecuta de manera local.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-699">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="d2ebf-700">Para mantener un número adecuado de variables de entorno en la lista representada por la aplicación, la aplicación filtrará las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-700">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="d2ebf-701">Vea el archivo *Pages/Index.cshtml.cs* de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-701">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="d2ebf-702">Para exponer todas las variables de entorno disponibles para la aplicación, cambie `FilteredConfiguration` en *Pages/Index.cshtml.cs* por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-702">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="d2ebf-703">Prefijos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-703">Prefixes</span></span>

<span data-ttu-id="d2ebf-704">Las variables de entorno que se cargan en la configuración de la aplicación se filtran cuando se proporciona un prefijo para el método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-704">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="d2ebf-705">Por ejemplo, para filtrar las variables de entorno en el prefijo `CUSTOM_`, suministre el prefijo para el proveedor de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-705">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="d2ebf-706">El prefijo se quita cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-706">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="d2ebf-707">Al crear el generador de host, las variables de entorno proporcionan la configuración del host.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-707">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="d2ebf-708">Para obtener más información sobre el prefijo usado para estas variables de entorno, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-708">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d2ebf-709">**Prefijos de cadena de conexión**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-709">**Connection string prefixes**</span></span>

<span data-ttu-id="d2ebf-710">La API de configuración tiene reglas de procesamiento especial para cuatro variables de entorno de cadena de conexión relacionadas con la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-710">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d2ebf-711">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación si no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-711">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d2ebf-712">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="d2ebf-712">Connection string prefix</span></span> | <span data-ttu-id="d2ebf-713">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-713">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d2ebf-714">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="d2ebf-714">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d2ebf-715">MySQL</span><span class="sxs-lookup"><span data-stu-id="d2ebf-715">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d2ebf-716">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="d2ebf-716">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d2ebf-717">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d2ebf-717">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d2ebf-718">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-718">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d2ebf-719">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-719">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d2ebf-720">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-720">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d2ebf-721">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="d2ebf-721">Environment variable key</span></span> | <span data-ttu-id="d2ebf-722">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="d2ebf-722">Converted configuration key</span></span> | <span data-ttu-id="d2ebf-723">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-723">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-724">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-724">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-725">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-726">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-726">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-727">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-728">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-728">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d2ebf-729">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d2ebf-730">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-730">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="d2ebf-731">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-731">**Example**</span></span>

<span data-ttu-id="d2ebf-732">Se crea una variable de entorno de una cadena de conexión personalizada en el servidor:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-732">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="d2ebf-733">Nombre: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-733">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="d2ebf-734">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-734">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="d2ebf-735">Si `IConfiguration` se inserta y se asigna a un campo denominado `_config`, se lee el valor siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-735">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="d2ebf-736">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-736">File Configuration Provider</span></span>

<span data-ttu-id="d2ebf-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d2ebf-738">Los proveedores de configuración siguientes están dedicados a determinados tipos de archivo:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-738">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="d2ebf-739">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="d2ebf-739">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d2ebf-740">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="d2ebf-740">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="d2ebf-741">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="d2ebf-741">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d2ebf-742">Proveedor de configuración de INI</span><span class="sxs-lookup"><span data-stu-id="d2ebf-742">INI Configuration Provider</span></span>

<span data-ttu-id="d2ebf-743"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-743">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-744">Para activar la configuración de archivo INI, llame al método de extensión <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-744">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d2ebf-745">Los dos puntos se pueden usar como un delimitador de sección en la configuración de archivo INI.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-745">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="d2ebf-746">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-746">Overloads permit specifying:</span></span>

* <span data-ttu-id="d2ebf-747">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-747">Whether the file is optional.</span></span>
* <span data-ttu-id="d2ebf-748">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-748">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d2ebf-749"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-749">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d2ebf-750">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-750">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d2ebf-751">Un ejemplo genérico de un archivo de configuración INI:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-751">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="d2ebf-752">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-752">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d2ebf-753">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-753">section0:key0</span></span>
* <span data-ttu-id="d2ebf-754">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-754">section0:key1</span></span>
* <span data-ttu-id="d2ebf-755">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-755">section1:subsection:key</span></span>
* <span data-ttu-id="d2ebf-756">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-756">section2:subsection0:key</span></span>
* <span data-ttu-id="d2ebf-757">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-757">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="d2ebf-758">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="d2ebf-758">JSON Configuration Provider</span></span>

<span data-ttu-id="d2ebf-759"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-759">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="d2ebf-760">Para activar la configuración de archivo JSON, llame al método de extensión <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-760">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d2ebf-761">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-761">Overloads permit specifying:</span></span>

* <span data-ttu-id="d2ebf-762">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-762">Whether the file is optional.</span></span>
* <span data-ttu-id="d2ebf-763">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-763">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d2ebf-764"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-764">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d2ebf-765">Se llama automáticamente a `AddJsonFile` dos veces cuando un nuevo generador de host se inicializa con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-765">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d2ebf-766">Se llama al método para cargar la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-766">The method is called to load configuration from:</span></span>

* <span data-ttu-id="d2ebf-767">*appsettings.json*: Este archivo se lee primero.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-767">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="d2ebf-768">La versión del entorno del archivo puede invalidar los valores que proporciona el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-768">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="d2ebf-769">*appsettings.{Environment}.json*: La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-769">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="d2ebf-770">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-770">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d2ebf-771">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-771">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d2ebf-772">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-772">Environment variables.</span></span>
* <span data-ttu-id="d2ebf-773">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-773">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d2ebf-774">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-774">Command-line arguments.</span></span>

<span data-ttu-id="d2ebf-775">El proveedor de configuración de JSON se establece en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-775">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="d2ebf-776">Por tanto, los secretos de usuario, las variables de entorno y los argumentos de la línea de comandos invalidan la configuración establecida por los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-776">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="d2ebf-777">Llame a `ConfigureAppConfiguration` al crear el host para especificar la configuración de la aplicación para archivos distintos de *appsettings.json* y *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-777">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d2ebf-778">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-778">**Example**</span></span>

<span data-ttu-id="d2ebf-779">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-779">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="d2ebf-780">La primera llamada a `AddJsonFile` carga la configuración desde *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-780">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="d2ebf-781">La segunda llamada a `AddJsonFile` carga la configuración desde *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-781">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="d2ebf-782">Para *appsettings.Development.json* en la aplicación de ejemplo, se carga el siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-782">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="d2ebf-783">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-783">Run the sample app.</span></span> <span data-ttu-id="d2ebf-784">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-784">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d2ebf-785">La salida contiene pares clave-valor para la configuración basada en el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-785">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="d2ebf-786">El nivel de registro de la clave `Logging:LogLevel:Default` es `Debug` al ejecutar la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-786">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="d2ebf-787">Vuelva a ejecutar la aplicación de ejemplo en el entorno de producción:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-787">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="d2ebf-788">Abra el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-788">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="d2ebf-789">En el perfil de `ConfigurationSample`, cambie el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` por `Production`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-789">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="d2ebf-790">Guarde el archivo y ejecute la aplicación con `dotnet run` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-790">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="d2ebf-791">La configuración de *appsettings.Development.json* ya no invalida la configuración de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-791">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="d2ebf-792">El nivel de registro de la clave `Logging:LogLevel:Default` es `Warning`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-792">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d2ebf-793">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="d2ebf-793">XML Configuration Provider</span></span>

<span data-ttu-id="d2ebf-794"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-794">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d2ebf-795">Para activar la configuración de archivo XML, llame al método de extensión <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-795">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d2ebf-796">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-796">Overloads permit specifying:</span></span>

* <span data-ttu-id="d2ebf-797">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-797">Whether the file is optional.</span></span>
* <span data-ttu-id="d2ebf-798">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-798">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d2ebf-799"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-799">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d2ebf-800">El nodo raíz del archivo de configuración se omite cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-800">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="d2ebf-801">No especifique una definición de tipo de documento (DTD) ni el espacio de nombres en el archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-801">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="d2ebf-802">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-802">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d2ebf-803">Los archivos de configuración XML pueden usar distintos nombres de elemento para las secciones repetidas:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-803">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="d2ebf-804">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-804">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d2ebf-805">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-805">section0:key0</span></span>
* <span data-ttu-id="d2ebf-806">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-806">section0:key1</span></span>
* <span data-ttu-id="d2ebf-807">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-807">section1:key0</span></span>
* <span data-ttu-id="d2ebf-808">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-808">section1:key1</span></span>

<span data-ttu-id="d2ebf-809">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-809">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="d2ebf-810">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-810">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d2ebf-811">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-811">section:section0:key:key0</span></span>
* <span data-ttu-id="d2ebf-812">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-812">section:section0:key:key1</span></span>
* <span data-ttu-id="d2ebf-813">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-813">section:section1:key:key0</span></span>
* <span data-ttu-id="d2ebf-814">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-814">section:section1:key:key1</span></span>

<span data-ttu-id="d2ebf-815">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-815">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d2ebf-816">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-816">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d2ebf-817">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d2ebf-817">key:attribute</span></span>
* <span data-ttu-id="d2ebf-818">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d2ebf-818">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d2ebf-819">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-819">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="d2ebf-820"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-820">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d2ebf-821">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-821">The key is the file name.</span></span> <span data-ttu-id="d2ebf-822">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-822">The value contains the file's contents.</span></span> <span data-ttu-id="d2ebf-823">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-823">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d2ebf-824">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-824">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d2ebf-825">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-825">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d2ebf-826">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-826">Overloads permit specifying:</span></span>

* <span data-ttu-id="d2ebf-827">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-827">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d2ebf-828">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-828">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d2ebf-829">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-829">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d2ebf-830">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-830">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d2ebf-831">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-831">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="d2ebf-832">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="d2ebf-832">Memory Configuration Provider</span></span>

<span data-ttu-id="d2ebf-833"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-833">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d2ebf-834">Para activar la configuración de colección en memoria, llame al método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-834">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d2ebf-835">El proveedor de configuración se puede inicializar con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-835">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="d2ebf-836">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-836">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="d2ebf-837">En el ejemplo siguiente, se crea un diccionario de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-837">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="d2ebf-838">El diccionario se usa con una llamada a `AddInMemoryCollection` para proporcionar la configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-838">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="d2ebf-839">GetValue</span><span class="sxs-lookup"><span data-stu-id="d2ebf-839">GetValue</span></span>

<span data-ttu-id="d2ebf-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado que no es de colección.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="d2ebf-841">Una sobrecarga acepta un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-841">An overload accepts a default value.</span></span>

<span data-ttu-id="d2ebf-842">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-842">The following example:</span></span>

* <span data-ttu-id="d2ebf-843">Se extrae el valor de cadena de la configuración con la clave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-843">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="d2ebf-844">Si `NumberKey` no se encuentra en las claves de configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-844">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="d2ebf-845">Se escribe el valor como `int`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-845">Types the value as an `int`.</span></span>
* <span data-ttu-id="d2ebf-846">Se almacena el valor en la propiedad `NumberConfig` para usarlo en la página.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-846">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d2ebf-847">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="d2ebf-847">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d2ebf-848">Para los ejemplos siguientes, considere el siguiente archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-848">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="d2ebf-849">Se encuentran cuatro claves en dos secciones, una de las cuales incluye un par de subsecciones:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-849">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="d2ebf-850">Cuando el archivo se lee en la configuración, se crean las siguientes claves jerárquicas únicas para contener los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-850">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="d2ebf-851">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-851">section0:key0</span></span>
* <span data-ttu-id="d2ebf-852">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-852">section0:key1</span></span>
* <span data-ttu-id="d2ebf-853">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-853">section1:key0</span></span>
* <span data-ttu-id="d2ebf-854">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-854">section1:key1</span></span>
* <span data-ttu-id="d2ebf-855">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-855">section2:subsection0:key0</span></span>
* <span data-ttu-id="d2ebf-856">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-856">section2:subsection0:key1</span></span>
* <span data-ttu-id="d2ebf-857">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-857">section2:subsection1:key0</span></span>
* <span data-ttu-id="d2ebf-858">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-858">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="d2ebf-859">GetSection</span><span class="sxs-lookup"><span data-stu-id="d2ebf-859">GetSection</span></span>

<span data-ttu-id="d2ebf-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrae una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d2ebf-861">Para devolver un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contiene los pares clave-valor en `section1`, llame a `GetSection` y suministre el nombre de la sección:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-861">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="d2ebf-862">`configSection` no tiene ningún valor, solo una clave y una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-862">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="d2ebf-863">De forma similar, para obtener los valores de las claves de `section2:subsection0`, llame a `GetSection` y suministre la ruta de acceso a la sección:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-863">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="d2ebf-864">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-864">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d2ebf-865">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-865">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d2ebf-866">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-866">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d2ebf-867">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-867">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="d2ebf-868">GetChildren</span><span class="sxs-lookup"><span data-stu-id="d2ebf-868">GetChildren</span></span>

<span data-ttu-id="d2ebf-869">Una llamada a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) en `section2` obtiene una `IEnumerable<IConfigurationSection>` que incluye:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-869">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="d2ebf-870">Existe</span><span class="sxs-lookup"><span data-stu-id="d2ebf-870">Exists</span></span>

<span data-ttu-id="d2ebf-871">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar si existe una sección de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-871">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="d2ebf-872">Dados los datos de ejemplo, `sectionExists` es `false` porque no hay una sección `section2:subsection2` en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-872">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="d2ebf-873">Enlazar a un gráfico de objetos</span><span class="sxs-lookup"><span data-stu-id="d2ebf-873">Bind to an object graph</span></span>

<span data-ttu-id="d2ebf-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> es capaz de enlazar todo un gráfico de objetos POCO.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="d2ebf-875">Al igual que ocurre con el enlace de un objeto simple, solo se enlazan las propiedades públicas de lectura o escritura.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-875">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="d2ebf-876">El ejemplo contiene un modelo `TvShow` cuyo gráfico de objetos incluye las clases `Metadata` y `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="d2ebf-876">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="d2ebf-877">La aplicación de ejemplo tiene un archivo *tvshow.xml* que contiene los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-877">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="d2ebf-878">Configuración está enlazada a todo el gráfico de objetos `TvShow`con el método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-878">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="d2ebf-879">La instancia enlazada se asigna a una propiedad para la representación:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-879">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="d2ebf-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d2ebf-881">`Get<T>` es más conveniente que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-881">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="d2ebf-882">En el código siguiente se muestra cómo usar `Get<T>` con el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-882">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d2ebf-883">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="d2ebf-883">Bind an array to a class</span></span>

<span data-ttu-id="d2ebf-884">*La aplicación de ejemplo muestra los conceptos que se explican en esta sección.*</span><span class="sxs-lookup"><span data-stu-id="d2ebf-884">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="d2ebf-885"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-885">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d2ebf-886">Cualquier formato de matriz que expone un segmento de clave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) es capaz de enlazar una matriz a una matriz de clase POCO.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-886">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="d2ebf-887">El enlace se proporciona por convención.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-887">Binding is provided by convention.</span></span> <span data-ttu-id="d2ebf-888">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-888">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="d2ebf-889">**Procesamiento de matriz en memoria**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-889">**In-memory array processing**</span></span>

<span data-ttu-id="d2ebf-890">Considere los valores y las claves de configuración que se muestran en esta tabla.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-890">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="d2ebf-891">Key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-891">Key</span></span>             | <span data-ttu-id="d2ebf-892">Valor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-892">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d2ebf-893">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-893">array:entries:0</span></span> | <span data-ttu-id="d2ebf-894">value0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-894">value0</span></span> |
| <span data-ttu-id="d2ebf-895">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-895">array:entries:1</span></span> | <span data-ttu-id="d2ebf-896">value1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-896">value1</span></span> |
| <span data-ttu-id="d2ebf-897">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-897">array:entries:2</span></span> | <span data-ttu-id="d2ebf-898">value2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-898">value2</span></span> |
| <span data-ttu-id="d2ebf-899">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-899">array:entries:4</span></span> | <span data-ttu-id="d2ebf-900">value4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-900">value4</span></span> |
| <span data-ttu-id="d2ebf-901">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="d2ebf-901">array:entries:5</span></span> | <span data-ttu-id="d2ebf-902">value5</span><span class="sxs-lookup"><span data-stu-id="d2ebf-902">value5</span></span> |

<span data-ttu-id="d2ebf-903">Estas claves y valores se cargan en la aplicación de ejemplo a través del proveedor de configuración de memoria:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-903">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="d2ebf-904">La matriz omite un valor de índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-904">The array skips a value for index &num;3.</span></span> <span data-ttu-id="d2ebf-905">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en los objetos enlazados, lo que queda claro cuando se muestra el resultado de enlazar esta matriz a un objeto.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="d2ebf-906">En la aplicación de ejemplo, hay disponible una clase POCO para almacenar los datos de configuración enlazados:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-906">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="d2ebf-907">Los datos de configuración están enlazados al objeto:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-907">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="d2ebf-908">También se puede usar la sintaxis [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*), lo que genera un código más compacto:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="d2ebf-909">El objeto enlazado, una instancia de `ArrayExample`, recibe los datos de la matriz desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-909">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="d2ebf-910">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-910">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d2ebf-911">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-911">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d2ebf-912">0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-912">0</span></span>                            | <span data-ttu-id="d2ebf-913">value0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-913">value0</span></span>                       |
| <span data-ttu-id="d2ebf-914">1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-914">1</span></span>                            | <span data-ttu-id="d2ebf-915">value1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-915">value1</span></span>                       |
| <span data-ttu-id="d2ebf-916">2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-916">2</span></span>                            | <span data-ttu-id="d2ebf-917">value2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-917">value2</span></span>                       |
| <span data-ttu-id="d2ebf-918">3</span><span class="sxs-lookup"><span data-stu-id="d2ebf-918">3</span></span>                            | <span data-ttu-id="d2ebf-919">value4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-919">value4</span></span>                       |
| <span data-ttu-id="d2ebf-920">4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-920">4</span></span>                            | <span data-ttu-id="d2ebf-921">value5</span><span class="sxs-lookup"><span data-stu-id="d2ebf-921">value5</span></span>                       |

<span data-ttu-id="d2ebf-922">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-922">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d2ebf-923">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración solo se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-923">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d2ebf-924">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-924">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d2ebf-925">El elemento de configuración omitido para el índice &num;3 se puede proporcionar antes del enlace a la instancia `ArrayExample` por cualquier proveedor de configuración que genera el par clave-valor correcto en la configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-925">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="d2ebf-926">Si el ejemplo incluía un proveedor de configuración JSON adicional con el par clave-valor omitido, `ArrayExample.Entries` coincide con la matriz de configuración completa:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-926">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="d2ebf-927">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-927">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="d2ebf-928">En `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-928">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="d2ebf-929">El par clave-valor que se muestra en la tabla se carga en la configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-929">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="d2ebf-930">Clave</span><span class="sxs-lookup"><span data-stu-id="d2ebf-930">Key</span></span>             | <span data-ttu-id="d2ebf-931">Valor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-931">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d2ebf-932">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="d2ebf-932">array:entries:3</span></span> | <span data-ttu-id="d2ebf-933">value3</span><span class="sxs-lookup"><span data-stu-id="d2ebf-933">value3</span></span> |

<span data-ttu-id="d2ebf-934">Si la instancia de clase `ArrayExample` se enlaza después de que el proveedor de configuración JSON incluye la entrada para el índice &num;3, la matriz `ArrayExample.Entries` incluye el valor.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-934">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="d2ebf-935">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-935">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d2ebf-936">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-936">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d2ebf-937">0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-937">0</span></span>                            | <span data-ttu-id="d2ebf-938">value0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-938">value0</span></span>                       |
| <span data-ttu-id="d2ebf-939">1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-939">1</span></span>                            | <span data-ttu-id="d2ebf-940">value1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-940">value1</span></span>                       |
| <span data-ttu-id="d2ebf-941">2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-941">2</span></span>                            | <span data-ttu-id="d2ebf-942">value2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-942">value2</span></span>                       |
| <span data-ttu-id="d2ebf-943">3</span><span class="sxs-lookup"><span data-stu-id="d2ebf-943">3</span></span>                            | <span data-ttu-id="d2ebf-944">value3</span><span class="sxs-lookup"><span data-stu-id="d2ebf-944">value3</span></span>                       |
| <span data-ttu-id="d2ebf-945">4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-945">4</span></span>                            | <span data-ttu-id="d2ebf-946">value4</span><span class="sxs-lookup"><span data-stu-id="d2ebf-946">value4</span></span>                       |
| <span data-ttu-id="d2ebf-947">5</span><span class="sxs-lookup"><span data-stu-id="d2ebf-947">5</span></span>                            | <span data-ttu-id="d2ebf-948">value5</span><span class="sxs-lookup"><span data-stu-id="d2ebf-948">value5</span></span>                       |

<span data-ttu-id="d2ebf-949">**Procesamiento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="d2ebf-949">**JSON array processing**</span></span>

<span data-ttu-id="d2ebf-950">Si un archivo JSON contiene una matriz, se crean claves de configuración para los elementos de la matriz con un índice de sección basado en cero.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-950">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="d2ebf-951">En el siguiente archivo de configuración, `subsection` es una matriz:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-951">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="d2ebf-952">El proveedor de configuración JSON lee los datos de configuración en los siguientes pares clave-valor:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-952">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="d2ebf-953">Key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-953">Key</span></span>                     | <span data-ttu-id="d2ebf-954">Valor</span><span class="sxs-lookup"><span data-stu-id="d2ebf-954">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="d2ebf-955">json_array:key</span><span class="sxs-lookup"><span data-stu-id="d2ebf-955">json_array:key</span></span>          | <span data-ttu-id="d2ebf-956">valueA</span><span class="sxs-lookup"><span data-stu-id="d2ebf-956">valueA</span></span> |
| <span data-ttu-id="d2ebf-957">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-957">json_array:subsection:0</span></span> | <span data-ttu-id="d2ebf-958">valueB</span><span class="sxs-lookup"><span data-stu-id="d2ebf-958">valueB</span></span> |
| <span data-ttu-id="d2ebf-959">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-959">json_array:subsection:1</span></span> | <span data-ttu-id="d2ebf-960">valueC</span><span class="sxs-lookup"><span data-stu-id="d2ebf-960">valueC</span></span> |
| <span data-ttu-id="d2ebf-961">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-961">json_array:subsection:2</span></span> | <span data-ttu-id="d2ebf-962">valueD</span><span class="sxs-lookup"><span data-stu-id="d2ebf-962">valueD</span></span> |

<span data-ttu-id="d2ebf-963">En la aplicación de ejemplo, la clase POCO siguiente está disponible para enlazar los pares clave-valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-963">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="d2ebf-964">Después del enlace, `JsonArrayExample.Key` contiene el valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-964">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="d2ebf-965">Los valores de la subsección se almacenan en la propiedad de la matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-965">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="d2ebf-966">Índice de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-966">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="d2ebf-967">Valor de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="d2ebf-967">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="d2ebf-968">0</span><span class="sxs-lookup"><span data-stu-id="d2ebf-968">0</span></span>                                   | <span data-ttu-id="d2ebf-969">valueB</span><span class="sxs-lookup"><span data-stu-id="d2ebf-969">valueB</span></span>                              |
| <span data-ttu-id="d2ebf-970">1</span><span class="sxs-lookup"><span data-stu-id="d2ebf-970">1</span></span>                                   | <span data-ttu-id="d2ebf-971">valueC</span><span class="sxs-lookup"><span data-stu-id="d2ebf-971">valueC</span></span>                              |
| <span data-ttu-id="d2ebf-972">2</span><span class="sxs-lookup"><span data-stu-id="d2ebf-972">2</span></span>                                   | <span data-ttu-id="d2ebf-973">valueD</span><span class="sxs-lookup"><span data-stu-id="d2ebf-973">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="d2ebf-974">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="d2ebf-974">Custom configuration provider</span></span>

<span data-ttu-id="d2ebf-975">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="d2ebf-975">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d2ebf-976">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-976">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d2ebf-977">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-977">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d2ebf-978">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-978">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d2ebf-979">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-979">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d2ebf-980">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-980">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d2ebf-981">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-981">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d2ebf-982">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-982">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d2ebf-983">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-983">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d2ebf-984">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-984">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d2ebf-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d2ebf-986">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-986">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d2ebf-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d2ebf-988">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-988">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d2ebf-989">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-989">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="d2ebf-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d2ebf-991">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-991">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d2ebf-992">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-992">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d2ebf-993">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-993">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="d2ebf-994">Acceso a la configuración durante el inicio</span><span class="sxs-lookup"><span data-stu-id="d2ebf-994">Access configuration during startup</span></span>

<span data-ttu-id="d2ebf-995">Inserte `IConfiguration` en el constructor `Startup` para acceder a los valores de configuración en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-995">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d2ebf-996">Para acceder a la configuración en `Startup.Configure`, inserte `IConfiguration` directamente en el método o use la instancia desde el constructor:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-996">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="d2ebf-997">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="d2ebf-997">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="d2ebf-998">Acceso a la configuración en una página de Razor Pages o en una vista de MVC</span><span class="sxs-lookup"><span data-stu-id="d2ebf-998">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="d2ebf-999">Para acceder a los valores de configuración en una página de Razor Pages o una vista de MVC, agregue una [directiva using](xref:mvc/views/razor#using) ([Referencia de C#: directiva using](/dotnet/csharp/language-reference/keywords/using-directive)) para el [espacio de nombres Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inyecte <xref:Microsoft.Extensions.Configuration.IConfiguration> en la página o en la vista.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-999">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="d2ebf-1000">En una página de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1000">In a Razor Pages page:</span></span>

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

<span data-ttu-id="d2ebf-1001">En una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1001">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d2ebf-1002">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1002">Add configuration from an external assembly</span></span>

<span data-ttu-id="d2ebf-1003">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1003">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d2ebf-1004">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1004">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d2ebf-1005">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d2ebf-1005">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end