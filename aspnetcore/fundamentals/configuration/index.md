---
title: Configuración en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la API de configuración para una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536329"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="2f0cd-103">Configuración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f0cd-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="2f0cd-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f0cd-105">La configuración de ASP.NET Core se realiza mediante uno o varios [proveedores de configuración](#cp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="2f0cd-106">Los proveedores de configuración leen los datos sobre los ajustes de los pares clave-valor mediante distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="2f0cd-107">Archivos de configuración, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2f0cd-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="2f0cd-108">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-108">Environment variables</span></span>
* <span data-ttu-id="2f0cd-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-109">Azure Key Vault</span></span>
* <span data-ttu-id="2f0cd-110">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="2f0cd-110">Azure App Configuration</span></span>
* <span data-ttu-id="2f0cd-111">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-111">Command-line arguments</span></span>
* <span data-ttu-id="2f0cd-112">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="2f0cd-113">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="2f0cd-113">Directory files</span></span>
* <span data-ttu-id="2f0cd-114">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-114">In-memory .NET objects</span></span>

<span data-ttu-id="2f0cd-115">En este tema se proporciona información sobre la configuración de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="2f0cd-116">Para más información sobre el uso de la configuración en las aplicaciones de consola, consulte [Configuración de .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="2f0cd-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f0cd-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="2f0cd-118">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-118">Default configuration</span></span>

<span data-ttu-id="2f0cd-119">Las aplicaciones web de ASP.NET Core creadas con [dotnet new](/dotnet/core/tools/dotnet-new) o con Visual Studio generan el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="2f0cd-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="2f0cd-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  agrega un elemento `IConfiguration` existente como origen.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="2f0cd-122">En el caso de una configuración predeterminada, agrega la configuración del [host](#hvac) y lo establece como el primer origen para la configuración de la _aplicación_.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="2f0cd-123">[appsettings.json](#appsettingsjson) con el proveedor de configuración [JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="2f0cd-124">*appsettings.* `Environment` *.json* con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="2f0cd-125">Por ejemplo, *appsettings*.***Production\*\*_._json* y *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="2f0cd-126">[Secretos de la aplicación](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="2f0cd-127">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2f0cd-128">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="2f0cd-129">Los proveedores de configuración que se agregan posteriormente invalidan los ajustes de configuración de la clave anteriores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="2f0cd-130">Por ejemplo, si `MyKey` se establece tanto en *appsettings.json* como en el entorno, se usará el valor del entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="2f0cd-131">Con los proveedores de configuración predeterminados, el [proveedor de configuración de línea de comandos](#clcp) reemplaza al resto de proveedores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="2f0cd-132">Para obtener más información sobre `CreateDefaultBuilder`, consulte el artículo [Configuración predeterminada del generador](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="2f0cd-133">El código siguiente muestra los proveedores de configuración habilitados en el orden en el que se han agregado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="2f0cd-134">Fíjese en el siguiente archivo *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="2f0cd-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2f0cd-135">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-136">El elemento <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> predeterminado carga la configuración en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="2f0cd-137">*appsettings.* `Environment` *.json*: Por ejemplo, los archivos *appsettings*.***Production\*\*_._json* y *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="2f0cd-138">La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="2f0cd-139">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2f0cd-140">Los valores de *appsettings*.`Environment`.*json* invalidan las claves de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="2f0cd-141">Por ejemplo, de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-141">For example, by default:</span></span>

* <span data-ttu-id="2f0cd-142">En el desarrollo, la configuración de *appsettings*.***Development** _._json* sobrescribe los valores que se encuentran en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="2f0cd-143">En la producción, la configuración de *appsettings*.***Production** _._json* sobrescribe los valores que se encuentran en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="2f0cd-144">Por ejemplo, al implementar la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="2f0cd-145">Enlace de datos de configuración jerárquica mediante el patrón de opciones</span><span class="sxs-lookup"><span data-stu-id="2f0cd-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="2f0cd-146">Si usa la configuración [predeterminada](#default), los archivos *appsettings.json* and *appsettings.* `Environment` *.json* están habilitados con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="2f0cd-147">Los cambios realizados en *appsettings.json* y el archivo *appsettings.* `Environment` *.json* ***después*** de iniciar la aplicación los lee el [proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2f0cd-148">Consulte la sección [Proveedor de configuración JSON](#jcp) en este artículo para obtener información sobre cómo agregar archivos de configuración JSON adicionales.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="2f0cd-149">Combinación de colecciones de servicios</span><span class="sxs-lookup"><span data-stu-id="2f0cd-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="2f0cd-150">Seguridad y secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="2f0cd-150">Security and user secrets</span></span>

<span data-ttu-id="2f0cd-151">Directrices para los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="2f0cd-152">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="2f0cd-153">Se puede usar la herramienta [Administrador de secretos](xref:security/app-secrets) para almacenar secretos en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="2f0cd-154">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2f0cd-155">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2f0cd-156">De [forma predeterminada](#default), el origen de configuración de los secretos de usuario se registra después de los orígenes de configuración de JSON.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="2f0cd-157">Por lo tanto, las claves de secretos de usuario tienen prioridad sobre las claves de *appsettings.json* y *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="2f0cd-158">Para obtener más información sobre cómo almacenar contraseñas u otros datos confidenciales consulte:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2f0cd-159"><xref:security/app-secrets>: incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2f0cd-160">La herramienta [Administrador de secretos](#fcp) usa el proveedor de configuración de archivo para almacenar secretos de usuario en un archivo JSON en el sistema local.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="2f0cd-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2f0cd-162">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="2f0cd-163">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-163">Environment variables</span></span>

<span data-ttu-id="2f0cd-164">Al usar la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de los pares clave-valor de la variable de entorno después de leer *appsettings.json* , *appsettings.* `Environment` *.json* y los [secretos de usuario](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="2f0cd-165">Por lo tanto, los valores de clave que se leen del entorno reemplazan los valores que se leen de *appsettings.json* , *appsettings.* `Environment` *.json* y los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2f0cd-166">Los siguientes comandos `set`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-166">The following `set` commands:</span></span>

* <span data-ttu-id="2f0cd-167">Establecen las claves de entorno y los valores del [ejemplo anterior](#appsettingsjson) en Windows.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="2f0cd-168">Prueban la configuración al usar la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="2f0cd-169">El comando `dotnet run` debe ejecutarse en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="2f0cd-170">Los ajustes de configuración del entorno anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-170">The preceding environment settings:</span></span>

* <span data-ttu-id="2f0cd-171">Solo se establecen en procesos iniciados desde la ventana de comandos en la que se establecieron.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="2f0cd-172">No los podrán leer los exploradores que se inician con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="2f0cd-173">Los siguientes comandos [setx](/windows-server/administration/windows-commands/setx) se pueden usar para establecer las claves de entorno y los valores en Windows.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="2f0cd-174">A diferencia de `set`, la configuración de `setx` se conserva.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="2f0cd-175">`/M` establece la variable en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="2f0cd-176">Si no se usa el modificador `/M`, se establece una variable de entorno de usuario.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="2f0cd-177">Para comprobar que los comandos anteriores invalidan *appsettings.json* y *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="2f0cd-178">Con Visual Studio: salga y reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="2f0cd-179">Con la CLI: abra una nueva ventana de comandos y escriba `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="2f0cd-180">Llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una cadena para especificar un prefijo para las variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="2f0cd-181">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-181">In the preceding code:</span></span>

* <span data-ttu-id="2f0cd-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2f0cd-183">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="2f0cd-184">Las variables de entorno establecidas con el prefijo `MyCustomPrefix_` invalidan los proveedores de configuración [predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="2f0cd-185">Esto incluye las variables de entorno sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="2f0cd-186">El prefijo se quita cuando se leen los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="2f0cd-187">Los siguientes comandos prueban el prefijo personalizado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="2f0cd-188">La [configuración predeterminada](#default) carga las variables de entorno y los argumentos de la línea de comandos con los prefijos `DOTNET_` y `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="2f0cd-189">ASP.NET Core usa los prefijos `DOTNET_` y `ASPNETCORE_` para la [configuración del host y la aplicación](xref:fundamentals/host/generic-host#host-configuration), pero no para la del usuario.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="2f0cd-190">Para obtener más información sobre la configuración del host y de la aplicación, consulte el artículo [Host genérico de .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="2f0cd-191">En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="2f0cd-192">Los ajustes de configuración de Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="2f0cd-193">Se cifran en reposo y se transmiten a través de un canal cifrado.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="2f0cd-194">Se exponen como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-194">Exposed as environment variables.</span></span>

<span data-ttu-id="2f0cd-195">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2f0cd-196">Consulte la sección [Prefijos de cadena de conexión](#constr) para obtener información sobre las cadenas de conexión de base de datos de Azure.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="2f0cd-197">Nomenclatura de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-197">Naming of environment variables</span></span>

<span data-ttu-id="2f0cd-198">Los nombres de las variables de entorno reflejan la estructura de un archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="2f0cd-199">Cada elemento de la jerarquía está separado por un carácter de subrayado doble (preferible) o un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="2f0cd-200">Cuando la estructura del elemento incluye una matriz, el índice de la matriz se debe tratar como un nombre de elemento adicional en esta ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="2f0cd-201">Considere el siguiente archivo *appsettings.json* y sus valores equivalentes representados como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="2f0cd-202">**variables de entorno**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="2f0cd-203">Variables de entorno configuradas en el archivo launchSettings.json generado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="2f0cd-204">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="2f0cd-205">Por ejemplo, las plantillas web ASP.NET Core generan un archivo *launchSettings.json* que establece la configuración del punto de conexión en:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="2f0cd-206">Al configurar `applicationUrl`, se establece la variable de entorno `ASPNETCORE_URLS` y se invalidan los valores establecidos en el entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="2f0cd-207">Variables de entorno de escape en Linux</span><span class="sxs-lookup"><span data-stu-id="2f0cd-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="2f0cd-208">En Linux, el valor de las variables de entorno de URL debe ser de escape, para que `systemd` pueda analizarlas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="2f0cd-209">Uso de la herramienta de Linux `systemd-escape` que produce `http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="2f0cd-210">Visualización de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-210">Display environment variables</span></span>

<span data-ttu-id="2f0cd-211">En el código siguiente se muestran las variables de entorno y los valores en el inicio de la aplicación, lo que puede resultar útil al depurar la configuración del entorno:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="2f0cd-212">Línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-212">Command-line</span></span>

<span data-ttu-id="2f0cd-213">Si se usa la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de los pares de clave-valor de argumento de la línea de comandos después de los siguientes orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="2f0cd-214">Los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2f0cd-215">[Secretos de aplicaciones](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2f0cd-216">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-216">Environment variables.</span></span>

<span data-ttu-id="2f0cd-217">De [forma predeterminada](#default), los valores de configuración establecidos en la línea de comandos reemplazan los valores de configuración establecidos con el resto de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="2f0cd-218">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-218">Command-line arguments</span></span>

<span data-ttu-id="2f0cd-219">El comando siguiente establece las claves y los valores mediante `=`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="2f0cd-220">El comando siguiente establece las claves y los valores mediante `/`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="2f0cd-221">El comando siguiente establece las claves y los valores mediante `--`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="2f0cd-222">El valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-222">The key value:</span></span>

* <span data-ttu-id="2f0cd-223">Debe seguir a un signo `=`, o bien la clave debe tener un prefijo `--` o `/` cuando el valor sigue a un espacio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="2f0cd-224">No es necesario si se usa `=`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="2f0cd-225">Por ejemplo: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="2f0cd-226">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan `=` con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="2f0cd-227">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="2f0cd-227">Switch mappings</span></span>

<span data-ttu-id="2f0cd-228">Las asignaciones de modificador admiten la lógica de sustitución de nombres de **clave**.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="2f0cd-229">Proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2f0cd-230">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2f0cd-231">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2f0cd-232">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2f0cd-233">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2f0cd-234">Los modificadores deben comenzar con `-` o `--`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="2f0cd-235">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2f0cd-236">Para usar un diccionario de asignaciones de modificador, páselo a la llamada a `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="2f0cd-237">El código siguiente muestra los valores de clave para las claves reemplazadas:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-238">El siguiente comando sirve para probar el reemplazo de claves:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2f0cd-239">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2f0cd-240">En la llamada de `AddCommandLine` al método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2f0cd-241">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2f0cd-242">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="2f0cd-242">Hierarchical configuration data</span></span>

<span data-ttu-id="2f0cd-243">La API de configuración lee los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2f0cd-244">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="2f0cd-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2f0cd-245">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-246">La mejor manera de leer datos de configuración jerárquica es usar el patrón de opciones.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="2f0cd-247">Para obtener más información, vea la sección [Enlace de datos de configuración jerárquica](#optpat) en este documento.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="2f0cd-248">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2f0cd-249">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="2f0cd-250">Claves y valores de configuración</span><span class="sxs-lookup"><span data-stu-id="2f0cd-250">Configuration keys and values</span></span>

<span data-ttu-id="2f0cd-251">Las claves de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-251">Configuration keys:</span></span>

* <span data-ttu-id="2f0cd-252">No distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-252">Are case-insensitive.</span></span> <span data-ttu-id="2f0cd-253">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2f0cd-254">Si se establece una clave y un valor en más de un proveedor de configuración, se usa el valor del último proveedor agregado.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="2f0cd-255">Para obtener más información, vea la sección [Configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="2f0cd-256">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="2f0cd-256">Hierarchical keys</span></span>
  * <span data-ttu-id="2f0cd-257">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2f0cd-258">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2f0cd-259">Todas las plataformas admiten un carácter de subrayado doble, `__`, que se convierte automáticamente en un signo de dos puntos `:`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="2f0cd-260">En Azure Key Vault, las claves jerárquicas usan `--` como separador.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="2f0cd-261">El [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) reemplaza automáticamente `--` con `:` cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2f0cd-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2f0cd-263">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#boa).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="2f0cd-264">Los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-264">Configuration values:</span></span>

* <span data-ttu-id="2f0cd-265">Son cadenas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-265">Are strings.</span></span>
* <span data-ttu-id="2f0cd-266">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="2f0cd-267">Proveedores de configuración</span><span class="sxs-lookup"><span data-stu-id="2f0cd-267">Configuration providers</span></span>

<span data-ttu-id="2f0cd-268">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2f0cd-269">Proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-269">Provider</span></span> | <span data-ttu-id="2f0cd-270">Proporciona la configuración de</span><span class="sxs-lookup"><span data-stu-id="2f0cd-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="2f0cd-271">Proveedor de configuración de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="2f0cd-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="2f0cd-273">Proveedor de configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="2f0cd-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="2f0cd-274">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="2f0cd-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="2f0cd-275">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="2f0cd-276">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-276">Command-line parameters</span></span> |
| [<span data-ttu-id="2f0cd-277">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2f0cd-278">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-278">Custom source</span></span> |
| [<span data-ttu-id="2f0cd-279">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="2f0cd-280">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-280">Environment variables</span></span> |
| [<span data-ttu-id="2f0cd-281">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2f0cd-282">Archivos INI, JSON y XML</span><span class="sxs-lookup"><span data-stu-id="2f0cd-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="2f0cd-283">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2f0cd-284">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="2f0cd-284">Directory files</span></span> |
| [<span data-ttu-id="2f0cd-285">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2f0cd-286">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-286">In-memory collections</span></span> |
| [<span data-ttu-id="2f0cd-287">Secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="2f0cd-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="2f0cd-288">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="2f0cd-288">File in the user profile directory</span></span> |

<span data-ttu-id="2f0cd-289">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="2f0cd-290">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2f0cd-291">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="2f0cd-292">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="2f0cd-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="2f0cd-293">Secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="2f0cd-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="2f0cd-294">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2f0cd-295">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="2f0cd-296">Una práctica común es agregar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2f0cd-297">La secuencia de proveedores anterior se usa en la [configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="2f0cd-298">Prefijos de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="2f0cd-298">Connection string prefixes</span></span>

<span data-ttu-id="2f0cd-299">La API de configuración tiene reglas de procesamiento especiales para cuatro variables de entorno de cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="2f0cd-300">Estas cadenas de conexión están implicadas en la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2f0cd-301">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación con la [configuración predeterminada](#default) o cuando no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2f0cd-302">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="2f0cd-302">Connection string prefix</span></span> | <span data-ttu-id="2f0cd-303">Proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2f0cd-304">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2f0cd-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="2f0cd-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2f0cd-306">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="2f0cd-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2f0cd-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2f0cd-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2f0cd-308">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2f0cd-309">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2f0cd-310">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2f0cd-311">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-311">Environment variable key</span></span> | <span data-ttu-id="2f0cd-312">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="2f0cd-312">Converted configuration key</span></span> | <span data-ttu-id="2f0cd-313">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-314">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-315">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-316">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-317">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-318">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-319">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-320">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="2f0cd-321">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-321">File configuration provider</span></span>

<span data-ttu-id="2f0cd-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2f0cd-323">Los siguientes proveedores de configuración se derivan de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="2f0cd-324">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="2f0cd-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2f0cd-325">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="2f0cd-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="2f0cd-326">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="2f0cd-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2f0cd-327">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="2f0cd-327">INI configuration provider</span></span>

<span data-ttu-id="2f0cd-328"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-329">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="2f0cd-330">En el código anterior, se reemplaza la configuración de los archivos *MyIniConfig.ini* y *MyIniConfig*.`Environment`.*ini* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2f0cd-331">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2f0cd-332">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2f0cd-333">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="2f0cd-334">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="2f0cd-335">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="2f0cd-335">JSON configuration provider</span></span>

<span data-ttu-id="2f0cd-336"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="2f0cd-337">Las sobrecargas pueden especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-337">Overloads can specify:</span></span>

* <span data-ttu-id="2f0cd-338">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-338">Whether the file is optional.</span></span>
* <span data-ttu-id="2f0cd-339">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="2f0cd-340">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="2f0cd-341">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-341">The preceding code:</span></span>

* <span data-ttu-id="2f0cd-342">Configura el proveedor de configuración JSON para que cargue el archivo *MyConfig.json* con las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="2f0cd-343">`optional: true`: el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="2f0cd-344">`reloadOnChange: true`: el archivo se recarga cuando se guardan los cambios.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="2f0cd-345">Lee los [proveedores de configuración predeterminados](#default) antes que el archivo *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="2f0cd-346">La configuración del archivo *MyConfig.json* invalida la de los proveedores de configuración predeterminados, incluidos el [proveedor de configuración de variables de entorno](#evcp) y el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2f0cd-347">Normalmente ***no*** querrá que un archivo JSON personalizado invalide los valores establecidos en el [proveedor de configuración de variables de entorno](#evcp) ni en el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2f0cd-348">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="2f0cd-349">En el código anterior, la configuración de los archivos *MyConfig.json* y *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="2f0cd-350">Invalida la configuración de los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2f0cd-351">Es reemplazada por la configuración del [proveedor de configuración de variables de entorno](#evcp) y del [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2f0cd-352">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="2f0cd-353">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="2f0cd-354">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="2f0cd-354">XML configuration provider</span></span>

<span data-ttu-id="2f0cd-355"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-356">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="2f0cd-357">En el código anterior, se reemplaza la configuración de los archivos *MyXMLFile.xml* y *MyXMLFile*.`Environment`.*xml* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2f0cd-358">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2f0cd-359">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2f0cd-360">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="2f0cd-361">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-362">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="2f0cd-363">El código siguiente lee el archivo de configuración anterior y muestra las claves y los valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-364">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2f0cd-365">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2f0cd-366">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2f0cd-366">key:attribute</span></span>
* <span data-ttu-id="2f0cd-367">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2f0cd-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2f0cd-368">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="2f0cd-369"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2f0cd-370">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-370">The key is the file name.</span></span> <span data-ttu-id="2f0cd-371">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-371">The value contains the file's contents.</span></span> <span data-ttu-id="2f0cd-372">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2f0cd-373">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2f0cd-374">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2f0cd-375">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="2f0cd-376">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2f0cd-377">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2f0cd-378">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2f0cd-379">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2f0cd-380">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="2f0cd-381">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-381">Memory configuration provider</span></span>

<span data-ttu-id="2f0cd-382"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2f0cd-383">El código siguiente agrega una colección en memoria al sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="2f0cd-384">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra las opciones de configuración anteriores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-385">En el código anterior, `config.AddInMemoryCollection(Dict)` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2f0cd-386">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2f0cd-387">Consulte la sección [Enlace de matrices](#boa) para ver otro ejemplo que usa `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="2f0cd-388">Configuración del punto de conexión de Kestrel</span><span class="sxs-lookup"><span data-stu-id="2f0cd-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="2f0cd-389">La configuración del punto de conexión específico de Kestrel invalida todas las configuraciones del punto de conexión [entre servidores](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="2f0cd-390">Las configuraciones del punto de conexión entre servidores incluyen:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="2f0cd-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="2f0cd-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="2f0cd-392">`--urls` en la [línea de comandos](xref:fundamentals/configuration/index#command-line)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="2f0cd-393">La [variable de entorno](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="2f0cd-394">Tenga en cuenta el archivo siguiente *appsettings.json* usado en una aplicación web de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="2f0cd-395">Cuando el marcado resaltado anterior se usa en una aplicación web de ASP.NET Core ***y*** la aplicación se inicia en la línea de comandos con la siguiente configuración del punto de conexión entre servidores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="2f0cd-396">Kestrel se enlaza con el punto de conexión configurado específicamente para él en el archivo *appsettings.json* (`https://localhost:9999`) y no `https://localhost:7777`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="2f0cd-397">Considere el punto de conexión específico de Kestrel configurado como una variable de entorno:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="2f0cd-398">En la variable de entorno anterior, `Https` es el nombre del punto de conexión específico de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="2f0cd-399">En el archivo *appsettings.json* anterior también se define un punto de conexión específico de Kestrel denominado `Https`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="2f0cd-400">De [manera predeterminada](#default-configuration), las variables de entorno que utilizan el [proveedor de configuración de variables de entorno](#evcp) se leen después de *appsettings.* `Environment` *.json*, por lo tanto, la variable de entorno anterior se usa para el punto de conexión `Https`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="2f0cd-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="2f0cd-401">GetValue</span></span>

<span data-ttu-id="2f0cd-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-403">En el código anterior, si `NumberKey` no se encuentra en la configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2f0cd-404">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="2f0cd-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2f0cd-405">Para los ejemplos que aparecen a continuación, considere el siguiente archivo *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="2f0cd-406">El código siguiente agrega *MySubsection.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="2f0cd-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="2f0cd-407">GetSection</span></span>

<span data-ttu-id="2f0cd-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) devuelve una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2f0cd-409">El código siguiente devuelve los valores de `section1`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-410">El código siguiente devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-411">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2f0cd-412">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2f0cd-413">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2f0cd-414">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="2f0cd-415">GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="2f0cd-415">GetChildren and Exists</span></span>

<span data-ttu-id="2f0cd-416">El código siguiente llama a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) y devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-417">El código anterior llama a [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para comprobar que la sección existe:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="2f0cd-418">Enlace de matrices</span><span class="sxs-lookup"><span data-stu-id="2f0cd-418">Bind an array</span></span>

<span data-ttu-id="2f0cd-419">El método [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) permite enlazar matrices a objetos mediante el uso de los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2f0cd-420">Cualquier formato de matriz que exponga un segmento de clave numérica es capaz de enlazar una matriz a una matriz de clase [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="2f0cd-421">Tenga en cuenta este archivo *MyArray.json* de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="2f0cd-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="2f0cd-422">El código siguiente agrega *MyArray.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="2f0cd-423">El código siguiente lee la configuración y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-424">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="2f0cd-425">En el resultado anterior, el índice 3 tiene el valor `value40`, que corresponde a `"4": "value40",` en *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="2f0cd-426">Los índices de matriz enlazados son continuos y no están enlazados al índice de la clave de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="2f0cd-427">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en objetos enlazados.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="2f0cd-428">El código siguiente carga la configuración de `array:entries` con el método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="2f0cd-429">El código siguiente lee la configuración de `arrayDict` `Dictionary` y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-430">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="2f0cd-431">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2f0cd-432">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2f0cd-433">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2f0cd-434">Cualquier proveedor de configuración que lea el par clave-valor del índice &num;3 puede proporcionar el elemento de configuración omitido para el índice &num;3 antes del enlace a la instancia `ArrayExample`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="2f0cd-435">Fíjese en el siguiente archivo *Value3.json* de la descarga de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="2f0cd-436">En el código siguiente se incluye la configuración para *Value3.json* y `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="2f0cd-437">El código siguiente lee la configuración anterior y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-438">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="2f0cd-439">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="2f0cd-440">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-440">Custom configuration provider</span></span>

<span data-ttu-id="2f0cd-441">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2f0cd-442">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2f0cd-443">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2f0cd-444">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2f0cd-445">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2f0cd-446">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2f0cd-447">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2f0cd-448">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2f0cd-449">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2f0cd-450">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2f0cd-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2f0cd-452">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2f0cd-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2f0cd-454">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2f0cd-455">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="2f0cd-456">Puesto que las [claves de configuración no distinguen entre mayúsculas y minúsculas](#keys), el diccionario empleado para iniciar la base de datos se crea con el comparador que tampoco hace tal distinción ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="2f0cd-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2f0cd-458">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2f0cd-459">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2f0cd-460">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="2f0cd-461">Acceso a la configuración en métodos Startup</span><span class="sxs-lookup"><span data-stu-id="2f0cd-461">Access configuration in Startup</span></span>

<span data-ttu-id="2f0cd-462">En el código siguiente se muestran los datos de configuración de los métodos `Startup`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="2f0cd-463">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="2f0cd-464">Acceso a la configuración en Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2f0cd-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="2f0cd-465">En el código siguiente se muestran los datos de configuración en una página de RazorPages:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="2f0cd-466">En el siguiente código se agrega `MyOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="2f0cd-467">En el marcado siguiente se usa la directiva [`@inject`](xref:mvc/views/razor#inject) Razor para resolver y mostrar los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="2f0cd-468">Acceso a la configuración en un archivo de vista de MVC</span><span class="sxs-lookup"><span data-stu-id="2f0cd-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="2f0cd-469">En el código siguiente se muestran los datos de configuración de una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="2f0cd-470">Configurar opciones con un delegado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-470">Configure options with a delegate</span></span>

<span data-ttu-id="2f0cd-471">Las opciones configuradas en un delegado invalidan los valores establecidos en los proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="2f0cd-472">La configuración de opciones con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="2f0cd-473">En el código siguiente, se agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="2f0cd-474">Usa un delegado para configurar los valores de `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="2f0cd-475">En el código siguiente se muestran los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2f0cd-476">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appsettings.json* y, luego, se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2f0cd-477">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="2f0cd-477">Host versus app configuration</span></span>

<span data-ttu-id="2f0cd-478">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2f0cd-479">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2f0cd-480">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2f0cd-481">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2f0cd-482">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="2f0cd-483">Configuración predeterminada del host</span><span class="sxs-lookup"><span data-stu-id="2f0cd-483">Default host configuration</span></span>

<span data-ttu-id="2f0cd-484">Para obtener más información sobre la configuración predeterminada al usar el [host de web](xref:fundamentals/host/web-host), vea la [versión de este tema para ASP.NET Core 2.2](?view=aspnetcore-2.2&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="2f0cd-485">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2f0cd-486">Las variables de entorno con el prefijo `DOTNET_` (por ejemplo, `DOTNET_ENVIRONMENT`) mediante el [proveedor de configuración de variables de entorno](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="2f0cd-487">El prefijo (`DOTNET_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2f0cd-488">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2f0cd-489">La configuración predeterminada del host de web se ha establecido (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="2f0cd-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="2f0cd-490">Kestrel se usa como el servidor web y se ha configurado mediante los proveedores de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="2f0cd-491">Agregar el middleware de filtrado de host</span><span class="sxs-lookup"><span data-stu-id="2f0cd-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="2f0cd-492">Agregue el middleware de encabezados reenviados si la variable de entorno `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="2f0cd-493">Habilite la integración con IIS.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2f0cd-494">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="2f0cd-494">Other configuration</span></span>

<span data-ttu-id="2f0cd-495">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2f0cd-496">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2f0cd-497">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2f0cd-498">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2f0cd-499">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2f0cd-500">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2f0cd-501">Las variables de entorno configuradas en *launchSettings.json* invalidan aquellas configuradas en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="2f0cd-502">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2f0cd-503">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="2f0cd-504">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2f0cd-505">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f0cd-506">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2f0cd-506">Additional resources</span></span>

* [<span data-ttu-id="2f0cd-507">Configuración del código fuente</span><span class="sxs-lookup"><span data-stu-id="2f0cd-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f0cd-508">La configuración de la aplicación en ASP.NET Core se basa en pares clave-valor establecidos por *proveedores de configuración*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="2f0cd-509">Los proveedores de configuración leen los datos de configuración en los pares clave-valor de distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="2f0cd-510">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-510">Azure Key Vault</span></span>
* <span data-ttu-id="2f0cd-511">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="2f0cd-511">Azure App Configuration</span></span>
* <span data-ttu-id="2f0cd-512">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-512">Command-line arguments</span></span>
* <span data-ttu-id="2f0cd-513">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="2f0cd-514">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="2f0cd-514">Directory files</span></span>
* <span data-ttu-id="2f0cd-515">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-515">Environment variables</span></span>
* <span data-ttu-id="2f0cd-516">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-516">In-memory .NET objects</span></span>
* <span data-ttu-id="2f0cd-517">Archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="2f0cd-517">Settings files</span></span>

<span data-ttu-id="2f0cd-518">Los paquetes de configuración para escenarios de proveedor de configuración común ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) se incluyen en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2f0cd-519">Los ejemplos de código que siguen y en la aplicación de ejemplo utilizan el espacio de nombres <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="2f0cd-520">El *patrón de opciones* es una extensión de los conceptos de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="2f0cd-521">Las opciones usan clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="2f0cd-522">Para obtener más información, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2f0cd-523">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f0cd-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2f0cd-524">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="2f0cd-524">Host versus app configuration</span></span>

<span data-ttu-id="2f0cd-525">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2f0cd-526">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2f0cd-527">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2f0cd-528">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2f0cd-529">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2f0cd-530">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="2f0cd-530">Other configuration</span></span>

<span data-ttu-id="2f0cd-531">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2f0cd-532">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2f0cd-533">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2f0cd-534">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2f0cd-535">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2f0cd-536">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2f0cd-537">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="2f0cd-538">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-538">Default configuration</span></span>

<span data-ttu-id="2f0cd-539">Las aplicaciones web basadas en las plantillas de [dotnet new](/dotnet/core/tools/dotnet-new) de ASP.NET Core llaman a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> al crear un host.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="2f0cd-540">`CreateDefaultBuilder` proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="2f0cd-541">El contenido siguiente es válido para las aplicaciones que usen el [host de web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2f0cd-542">Para obtener más información sobre la configuración predeterminada al usar el [host genérico](xref:fundamentals/host/generic-host), vea la [versión más reciente de ese tema](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="2f0cd-543">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2f0cd-544">Variables de entorno prefijadas con `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`) con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2f0cd-545">El prefijo (`ASPNETCORE_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2f0cd-546">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2f0cd-547">La configuración de la aplicación la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="2f0cd-548">*appsettings.json* con el [proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2f0cd-549">*appsettings.{Entorno}.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2f0cd-550">[Secretos del usuario](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="2f0cd-551">Variables de entorno con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="2f0cd-552">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="2f0cd-553">Seguridad</span><span class="sxs-lookup"><span data-stu-id="2f0cd-553">Security</span></span>

<span data-ttu-id="2f0cd-554">Adopte los procedimientos siguientes para proteger los datos de configuración confidenciales:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="2f0cd-555">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="2f0cd-556">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2f0cd-557">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2f0cd-558">Para obtener más información, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2f0cd-559"><xref:security/app-secrets>: incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2f0cd-560">La herramienta Administrador de secretos usa el proveedor de configuración de archivo para almacenar secretos de usuario en un archivo JSON en el sistema local.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="2f0cd-561">El proveedor de configuración de archivo se describe más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="2f0cd-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2f0cd-563">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2f0cd-564">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="2f0cd-564">Hierarchical configuration data</span></span>

<span data-ttu-id="2f0cd-565">La API de configuración es capaz de mantener los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2f0cd-566">En el siguiente archivo JSON, hay cuatro claves en una estructura jerárquica de dos secciones:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="2f0cd-567">Cuando el archivo se lee en la configuración, se crean claves únicas para mantener la estructura de datos jerárquicos original del origen de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="2f0cd-568">Las secciones y claves se disminuyen con el uso de dos puntos (`:`) para mantener la estructura original:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="2f0cd-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-569">section0:key0</span></span>
* <span data-ttu-id="2f0cd-570">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-570">section0:key1</span></span>
* <span data-ttu-id="2f0cd-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-571">section1:key0</span></span>
* <span data-ttu-id="2f0cd-572">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-572">section1:key1</span></span>

<span data-ttu-id="2f0cd-573">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2f0cd-574">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="2f0cd-575">Convenciones</span><span class="sxs-lookup"><span data-stu-id="2f0cd-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="2f0cd-576">Orígenes y proveedores</span><span class="sxs-lookup"><span data-stu-id="2f0cd-576">Sources and providers</span></span>

<span data-ttu-id="2f0cd-577">Al iniciar la aplicación, los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="2f0cd-578">Los proveedores de configuración que implementan la detección de cambios tienen la capacidad de volver a cargar la configuración cuando se cambia una configuración subyacente.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="2f0cd-579">Por ejemplo, el proveedor de configuración de archivos (descrito más adelante en este tema) y el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) implementan la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="2f0cd-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponible en el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2f0cd-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> se puede insertar en Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obtener la configuración de la clase.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="2f0cd-582">En los ejemplos siguientes, se usa el campo `_config` para tener acceso a los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="2f0cd-583">Los proveedores de configuración no pueden usar la inserción de dependencias, porque no está disponible cuando el host los configura.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="2f0cd-584">Teclas</span><span class="sxs-lookup"><span data-stu-id="2f0cd-584">Keys</span></span>

<span data-ttu-id="2f0cd-585">Las claves de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="2f0cd-586">Las claves no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-586">Keys are case-insensitive.</span></span> <span data-ttu-id="2f0cd-587">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2f0cd-588">Si los mismos o distintos proveedores de configuración establecen un valor para la misma clave, el último valor establecido en la clave es el valor que se usa.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="2f0cd-589">Para más información sobre las claves JSON duplicadas, vea [este problema de GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="2f0cd-590">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="2f0cd-590">Hierarchical keys</span></span>
  * <span data-ttu-id="2f0cd-591">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2f0cd-592">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2f0cd-593">Todas las plataformas admiten un carácter de subrayado doble (`__`), que se convierte automáticamente en un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="2f0cd-594">En Azure Key Vault, las claves jerárquicas usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2f0cd-595">Escriba código para reemplazar los guiones por dos puntos cuando los secretos se carguen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2f0cd-596"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2f0cd-597">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="2f0cd-598">Valores</span><span class="sxs-lookup"><span data-stu-id="2f0cd-598">Values</span></span>

<span data-ttu-id="2f0cd-599">Los valores de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="2f0cd-600">Los valores son cadenas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-600">Values are strings.</span></span>
* <span data-ttu-id="2f0cd-601">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="2f0cd-602">Proveedores</span><span class="sxs-lookup"><span data-stu-id="2f0cd-602">Providers</span></span>

<span data-ttu-id="2f0cd-603">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2f0cd-604">Proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-604">Provider</span></span> | <span data-ttu-id="2f0cd-605">Proporciona la configuración de&hellip;</span><span class="sxs-lookup"><span data-stu-id="2f0cd-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="2f0cd-606">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="2f0cd-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-607">Azure Key Vault</span></span> |
| <span data-ttu-id="2f0cd-608">[Proveedor de Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="2f0cd-609">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="2f0cd-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="2f0cd-610">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="2f0cd-611">Parámetros de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-611">Command-line parameters</span></span> |
| [<span data-ttu-id="2f0cd-612">Proveedor de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2f0cd-613">Origen personalizado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-613">Custom source</span></span> |
| [<span data-ttu-id="2f0cd-614">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="2f0cd-615">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-615">Environment variables</span></span> |
| [<span data-ttu-id="2f0cd-616">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2f0cd-617">Archivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="2f0cd-618">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2f0cd-619">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="2f0cd-619">Directory files</span></span> |
| [<span data-ttu-id="2f0cd-620">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2f0cd-621">Colecciones en memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-621">In-memory collections</span></span> |
| <span data-ttu-id="2f0cd-622">[Secretos de usuario](xref:security/app-secrets) (temas de *Seguridad*)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="2f0cd-623">Archivo en el directorio del perfil de usuario</span><span class="sxs-lookup"><span data-stu-id="2f0cd-623">File in the user profile directory</span></span> |

<span data-ttu-id="2f0cd-624">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="2f0cd-625">En este tema, los proveedores de configuración se describen en orden alfabético y no en el orden en el que el código los organiza.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="2f0cd-626">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2f0cd-627">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2f0cd-628">Archivos ( *appsettings.json* y *appsettings.{Environment}.json*, donde `{Environment}` es el entorno de hospedaje actual de la aplicación)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="2f0cd-629">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2f0cd-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="2f0cd-630">[Secretos de usuario](xref:security/app-secrets) (solo para entornos de desarrollo)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="2f0cd-631">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-631">Environment variables</span></span>
1. <span data-ttu-id="2f0cd-632">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-632">Command-line arguments</span></span>

<span data-ttu-id="2f0cd-633">Una práctica común es colocar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2f0cd-634">La secuencia de proveedores anterior se usa cuando se inicializa un nuevo generador de host con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2f0cd-635">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="2f0cd-636">Configurar el generador de host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="2f0cd-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="2f0cd-637">Para configurar el generador de host, realice una llamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> en el generador de host con la configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="2f0cd-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2f0cd-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2f0cd-639">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar los proveedores de configuración de la aplicación además de aquellos que `CreateDefaultBuilder` agrega automáticamente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="2f0cd-640">Reemplazar la configuración anterior con argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="2f0cd-641">Para proporcionar configuración de aplicaciones que se pueda reemplazar por argumentos de la línea de comandos, llame a `AddCommandLine` en último lugar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="2f0cd-642">Quitar proveedores agregados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="2f0cd-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="2f0cd-643">Para quitar los proveedores agregados por `CreateDefaultBuilder`, llame primero a [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) en [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="2f0cd-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="2f0cd-644">Usar la configuración durante el inicio de la aplicación</span><span class="sxs-lookup"><span data-stu-id="2f0cd-644">Consume configuration during app startup</span></span>

<span data-ttu-id="2f0cd-645">La configuración proporcionada a la aplicación en `ConfigureAppConfiguration` está disponible durante el inicio de la aplicación, incluido `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f0cd-646">Para obtener más información, vea la sección [Acceso a la configuración durante el inicio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="2f0cd-647">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="2f0cd-648"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de pares clave-valor de argumento de la línea de comandos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-649">Para activar la configuración de línea de comandos, se llama al método de extensión <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2f0cd-650">`AddCommandLine` se llama automáticamente al llamar a `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="2f0cd-651">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2f0cd-652">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2f0cd-653">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2f0cd-654">[Secretos de usuario](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2f0cd-655">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-655">Environment variables.</span></span>

<span data-ttu-id="2f0cd-656">`CreateDefaultBuilder` agrega el proveedor de configuración de línea de comandos al final.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="2f0cd-657">Los argumentos de la línea de comandos que se pasan en tiempo de ejecución invalidan la configuración establecida por los otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="2f0cd-658">`CreateDefaultBuilder` actúa cuando se construye el host.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="2f0cd-659">Por tanto, la configuración de línea de comandos activada por `CreateDefaultBuilder` puede afectar la manera en que se configura el host.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="2f0cd-660">Para las aplicaciones basadas en plantillas de ASP.NET Core, `CreateDefaultBuilder` ya realiza una llamada a `AddCommandLine`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2f0cd-661">Para agregar proveedores de configuración adicionales y mantener la capacidad de reemplazar la configuración de sus proveedores con argumentos de la línea de comandos, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddCommandLine` en último lugar.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="2f0cd-662">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-662">**Example**</span></span>

<span data-ttu-id="2f0cd-663">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="2f0cd-664">Abra un símbolo del sistema en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="2f0cd-665">Proporcione un argumento de la línea de comandos para el comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="2f0cd-666">Una vez que se ejecuta la aplicación, abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2f0cd-667">Observe que la salida contiene el par clave-valor para el argumento de línea de comandos de configuración proporcionado a `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="2f0cd-668">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-668">Arguments</span></span>

<span data-ttu-id="2f0cd-669">El valor debe seguir a un signo igual (`=`) o la clave debe tener un prefijo (`--` o `/`) cuando el valor siga a un espacio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="2f0cd-670">El valor no es obligatorio si se usa un signo igual (por ejemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="2f0cd-671">Prefijo de la clave</span><span class="sxs-lookup"><span data-stu-id="2f0cd-671">Key prefix</span></span>               | <span data-ttu-id="2f0cd-672">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="2f0cd-673">Sin prefijo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="2f0cd-674">Dos guiones (`--`)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="2f0cd-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="2f0cd-676">Barra diagonal (`/`)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="2f0cd-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="2f0cd-678">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan un signo igual con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="2f0cd-679">Comandos de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="2f0cd-680">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="2f0cd-680">Switch mappings</span></span>

<span data-ttu-id="2f0cd-681">Las asignaciones de modificador admiten la lógica de sustitución de nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="2f0cd-682">Al crear manualmente la configuración con <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2f0cd-683">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2f0cd-684">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario (el reemplazo de la clave) para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2f0cd-685">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2f0cd-686">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2f0cd-687">Los modificadores deben empezar por un guion (`-`) o guion doble (`--`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="2f0cd-688">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2f0cd-689">Cree un diccionario de asignaciones de modificador.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="2f0cd-690">En el ejemplo siguiente, se crean dos asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="2f0cd-691">Al compilar el host, llame a `AddCommandLine` con el diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="2f0cd-692">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2f0cd-693">En la llamada de `AddCommandLine` del método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2f0cd-694">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino que permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="2f0cd-695">Después de crear el diccionario de asignaciones de modificador, contiene los datos que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="2f0cd-696">Clave</span><span class="sxs-lookup"><span data-stu-id="2f0cd-696">Key</span></span>       | <span data-ttu-id="2f0cd-697">Value</span><span class="sxs-lookup"><span data-stu-id="2f0cd-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="2f0cd-698">Si las claves de asignación de conmutador se usan al iniciar la aplicación, la configuración recibe el valor de configuración en la clave que el diccionario suministra:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="2f0cd-699">Una vez que se ejecuta el comando anterior, la configuración contiene los valores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="2f0cd-700">Key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-700">Key</span></span>               | <span data-ttu-id="2f0cd-701">Valor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="2f0cd-702">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="2f0cd-703"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de pares clave-valor de variables de entorno en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-704">Para activar la configuración de variables de entorno, llame al método de extensión <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2f0cd-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permite establecer variables de entorno en Azure Portal que pueden invalidar la configuración de la aplicación mediante el proveedor de configuración de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="2f0cd-706">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2f0cd-707">`AddEnvironmentVariables` se usa para cargar variables de entorno con el prefijo `ASPNETCORE_` para la [configuración de host](#host-versus-app-configuration) al inicializar un nuevo generador de host con el [host de web](xref:fundamentals/host/web-host) y al llamar a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="2f0cd-708">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2f0cd-709">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2f0cd-710">Configuración de la aplicación desde variables de entorno sin prefijo mediante la llamada a `AddEnvironmentVariables` sin prefijo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="2f0cd-711">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2f0cd-712">[Secretos de usuario](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2f0cd-713">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-713">Command-line arguments.</span></span>

<span data-ttu-id="2f0cd-714">El proveedor de configuración de variables de entorno se llama una vez establecida la configuración desde los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="2f0cd-715">Llamar al proveedor en esta posición permite que la lectura de las variables de entorno en tiempo de ejecución invaliden la configuración establecida por los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="2f0cd-716">Para proporcionar la configuración de la aplicación desde variables de entorno adicionales, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddEnvironmentVariables` con el prefijo:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="2f0cd-717">Llame a `AddEnvironmentVariables` en último lugar para permitir que las variables de entorno con el prefijo especificado invaliden los valores de otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="2f0cd-718">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-718">**Example**</span></span>

<span data-ttu-id="2f0cd-719">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="2f0cd-720">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-720">Run the sample app.</span></span> <span data-ttu-id="2f0cd-721">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2f0cd-722">Observe que el resultado contiene el par clave y valor para la variable de entorno `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="2f0cd-723">El valor refleja el entorno en el que se ejecuta la aplicación, habitualmente `Development` cuando se ejecuta de manera local.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="2f0cd-724">Para mantener un número adecuado de variables de entorno en la lista representada por la aplicación, la aplicación filtrará las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="2f0cd-725">Vea el archivo *Pages/Index.cshtml.cs* de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="2f0cd-726">Para exponer todas las variables de entorno disponibles para la aplicación, cambie `FilteredConfiguration` en *Pages/Index.cshtml.cs* por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="2f0cd-727">Prefijos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-727">Prefixes</span></span>

<span data-ttu-id="2f0cd-728">Las variables de entorno que se cargan en la configuración de la aplicación se filtran cuando se proporciona un prefijo para el método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="2f0cd-729">Por ejemplo, para filtrar las variables de entorno en el prefijo `CUSTOM_`, suministre el prefijo para el proveedor de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="2f0cd-730">El prefijo se quita cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="2f0cd-731">Al crear el generador de host, las variables de entorno proporcionan la configuración del host.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="2f0cd-732">Para obtener más información sobre el prefijo usado para estas variables de entorno, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2f0cd-733">**Prefijos de cadena de conexión**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-733">**Connection string prefixes**</span></span>

<span data-ttu-id="2f0cd-734">La API de configuración tiene reglas de procesamiento especial para cuatro variables de entorno de cadena de conexión relacionadas con la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2f0cd-735">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación si no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2f0cd-736">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="2f0cd-736">Connection string prefix</span></span> | <span data-ttu-id="2f0cd-737">Proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2f0cd-738">Proveedor personalizado</span><span class="sxs-lookup"><span data-stu-id="2f0cd-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2f0cd-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="2f0cd-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2f0cd-740">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="2f0cd-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2f0cd-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2f0cd-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2f0cd-742">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2f0cd-743">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2f0cd-744">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2f0cd-745">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="2f0cd-745">Environment variable key</span></span> | <span data-ttu-id="2f0cd-746">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="2f0cd-746">Converted configuration key</span></span> | <span data-ttu-id="2f0cd-747">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-748">Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-749">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-750">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-751">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-752">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2f0cd-753">Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2f0cd-754">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="2f0cd-755">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-755">**Example**</span></span>

<span data-ttu-id="2f0cd-756">Se crea una variable de entorno de una cadena de conexión personalizada en el servidor:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="2f0cd-757">Nombre: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="2f0cd-758">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="2f0cd-759">Si `IConfiguration` se inserta y se asigna a un campo denominado `_config`, se lee el valor siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="2f0cd-760">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-760">File Configuration Provider</span></span>

<span data-ttu-id="2f0cd-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2f0cd-762">Los proveedores de configuración siguientes están dedicados a determinados tipos de archivo:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="2f0cd-763">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="2f0cd-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2f0cd-764">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="2f0cd-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="2f0cd-765">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="2f0cd-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2f0cd-766">Proveedor de configuración de INI</span><span class="sxs-lookup"><span data-stu-id="2f0cd-766">INI Configuration Provider</span></span>

<span data-ttu-id="2f0cd-767"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-768">Para activar la configuración de archivo INI, llame al método de extensión <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2f0cd-769">Los dos puntos se pueden usar como un delimitador de sección en la configuración de archivo INI.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="2f0cd-770">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="2f0cd-771">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-771">Whether the file is optional.</span></span>
* <span data-ttu-id="2f0cd-772">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2f0cd-773"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2f0cd-774">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2f0cd-775">Un ejemplo genérico de un archivo de configuración INI:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="2f0cd-776">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2f0cd-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-777">section0:key0</span></span>
* <span data-ttu-id="2f0cd-778">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-778">section0:key1</span></span>
* <span data-ttu-id="2f0cd-779">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-779">section1:subsection:key</span></span>
* <span data-ttu-id="2f0cd-780">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-780">section2:subsection0:key</span></span>
* <span data-ttu-id="2f0cd-781">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="2f0cd-782">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="2f0cd-782">JSON Configuration Provider</span></span>

<span data-ttu-id="2f0cd-783"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="2f0cd-784">Para activar la configuración de archivo JSON, llame al método de extensión <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2f0cd-785">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="2f0cd-786">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-786">Whether the file is optional.</span></span>
* <span data-ttu-id="2f0cd-787">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2f0cd-788"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2f0cd-789">Se llama automáticamente a `AddJsonFile` dos veces cuando un nuevo generador de host se inicializa con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2f0cd-790">Se llama al método para cargar la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="2f0cd-791">*appsettings.json* : Este archivo se lee primero.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="2f0cd-792">La versión del entorno del archivo puede invalidar los valores que proporciona el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="2f0cd-793">*appsettings.{Environment}.json*: La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="2f0cd-794">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2f0cd-795">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2f0cd-796">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-796">Environment variables.</span></span>
* <span data-ttu-id="2f0cd-797">[Secretos de usuario](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2f0cd-798">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-798">Command-line arguments.</span></span>

<span data-ttu-id="2f0cd-799">El proveedor de configuración de JSON se establece en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="2f0cd-800">Por tanto, los secretos de usuario, las variables de entorno y los argumentos de la línea de comandos invalidan la configuración establecida por los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="2f0cd-801">Llame a `ConfigureAppConfiguration` al crear el host para especificar la configuración de la aplicación para archivos distintos de *appsettings.json* y *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2f0cd-802">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-802">**Example**</span></span>

<span data-ttu-id="2f0cd-803">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="2f0cd-804">La primera llamada a `AddJsonFile` carga la configuración desde *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="2f0cd-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="2f0cd-805">La segunda llamada a `AddJsonFile` carga la configuración desde *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="2f0cd-806">Para *appsettings.Development.json* en la aplicación de ejemplo, se carga el siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="2f0cd-807">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-807">Run the sample app.</span></span> <span data-ttu-id="2f0cd-808">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2f0cd-809">La salida contiene pares clave-valor para la configuración basada en el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="2f0cd-810">El nivel de registro de la clave `Logging:LogLevel:Default` es `Debug` al ejecutar la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="2f0cd-811">Vuelva a ejecutar la aplicación de ejemplo en el entorno de producción:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="2f0cd-812">Abra el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="2f0cd-813">En el perfil de `ConfigurationSample`, cambie el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` por `Production`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="2f0cd-814">Guarde el archivo y ejecute la aplicación con `dotnet run` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="2f0cd-815">La configuración de *appsettings.Development.json* ya no invalida la configuración de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="2f0cd-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="2f0cd-816">El nivel de registro de la clave `Logging:LogLevel:Default` es `Warning`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="2f0cd-817">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="2f0cd-817">XML Configuration Provider</span></span>

<span data-ttu-id="2f0cd-818"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2f0cd-819">Para activar la configuración de archivo XML, llame al método de extensión <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2f0cd-820">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="2f0cd-821">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-821">Whether the file is optional.</span></span>
* <span data-ttu-id="2f0cd-822">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2f0cd-823"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2f0cd-824">El nodo raíz del archivo de configuración se omite cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="2f0cd-825">No especifique una definición de tipo de documento (DTD) ni el espacio de nombres en el archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="2f0cd-826">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2f0cd-827">Los archivos de configuración XML pueden usar distintos nombres de elemento para las secciones repetidas:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="2f0cd-828">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2f0cd-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-829">section0:key0</span></span>
* <span data-ttu-id="2f0cd-830">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-830">section0:key1</span></span>
* <span data-ttu-id="2f0cd-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-831">section1:key0</span></span>
* <span data-ttu-id="2f0cd-832">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-832">section1:key1</span></span>

<span data-ttu-id="2f0cd-833">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="2f0cd-834">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2f0cd-835">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-835">section:section0:key:key0</span></span>
* <span data-ttu-id="2f0cd-836">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-836">section:section0:key:key1</span></span>
* <span data-ttu-id="2f0cd-837">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-837">section:section1:key:key0</span></span>
* <span data-ttu-id="2f0cd-838">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-838">section:section1:key:key1</span></span>

<span data-ttu-id="2f0cd-839">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2f0cd-840">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2f0cd-841">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2f0cd-841">key:attribute</span></span>
* <span data-ttu-id="2f0cd-842">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2f0cd-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2f0cd-843">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="2f0cd-844"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2f0cd-845">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-845">The key is the file name.</span></span> <span data-ttu-id="2f0cd-846">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-846">The value contains the file's contents.</span></span> <span data-ttu-id="2f0cd-847">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2f0cd-848">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2f0cd-849">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2f0cd-850">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="2f0cd-851">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2f0cd-852">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2f0cd-853">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2f0cd-854">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2f0cd-855">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="2f0cd-856">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="2f0cd-856">Memory Configuration Provider</span></span>

<span data-ttu-id="2f0cd-857"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2f0cd-858">Para activar la configuración de colección en memoria, llame al método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2f0cd-859">El proveedor de configuración se puede inicializar con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="2f0cd-860">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="2f0cd-861">En el ejemplo siguiente, se crea un diccionario de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="2f0cd-862">El diccionario se usa con una llamada a `AddInMemoryCollection` para proporcionar la configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="2f0cd-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="2f0cd-863">GetValue</span></span>

<span data-ttu-id="2f0cd-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado que no es de colección.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="2f0cd-865">Una sobrecarga acepta un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-865">An overload accepts a default value.</span></span>

<span data-ttu-id="2f0cd-866">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-866">The following example:</span></span>

* <span data-ttu-id="2f0cd-867">Se extrae el valor de cadena de la configuración con la clave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="2f0cd-868">Si `NumberKey` no se encuentra en las claves de configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="2f0cd-869">Se escribe el valor como `int`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="2f0cd-870">Se almacena el valor en la propiedad `NumberConfig` para usarlo en la página.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2f0cd-871">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="2f0cd-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2f0cd-872">Para los ejemplos siguientes, considere el siguiente archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="2f0cd-873">Se encuentran cuatro claves en dos secciones, una de las cuales incluye un par de subsecciones:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="2f0cd-874">Cuando el archivo se lee en la configuración, se crean las siguientes claves jerárquicas únicas para contener los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="2f0cd-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-875">section0:key0</span></span>
* <span data-ttu-id="2f0cd-876">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-876">section0:key1</span></span>
* <span data-ttu-id="2f0cd-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-877">section1:key0</span></span>
* <span data-ttu-id="2f0cd-878">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-878">section1:key1</span></span>
* <span data-ttu-id="2f0cd-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="2f0cd-880">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="2f0cd-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="2f0cd-882">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="2f0cd-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="2f0cd-883">GetSection</span></span>

<span data-ttu-id="2f0cd-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrae una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2f0cd-885">Para devolver un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contiene los pares clave-valor en `section1`, llame a `GetSection` y suministre el nombre de la sección:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="2f0cd-886">`configSection` no tiene ningún valor, solo una clave y una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="2f0cd-887">De forma similar, para obtener los valores de las claves de `section2:subsection0`, llame a `GetSection` y suministre la ruta de acceso a la sección:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="2f0cd-888">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2f0cd-889">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2f0cd-890">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2f0cd-891">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="2f0cd-892">GetChildren</span><span class="sxs-lookup"><span data-stu-id="2f0cd-892">GetChildren</span></span>

<span data-ttu-id="2f0cd-893">Una llamada a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) en `section2` obtiene una `IEnumerable<IConfigurationSection>` que incluye:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="2f0cd-894">Existe</span><span class="sxs-lookup"><span data-stu-id="2f0cd-894">Exists</span></span>

<span data-ttu-id="2f0cd-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar si existe una sección de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="2f0cd-896">Dados los datos de ejemplo, `sectionExists` es `false` porque no hay una sección `section2:subsection2` en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="2f0cd-897">Enlazar a un gráfico de objetos</span><span class="sxs-lookup"><span data-stu-id="2f0cd-897">Bind to an object graph</span></span>

<span data-ttu-id="2f0cd-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> es capaz de enlazar todo un gráfico de objetos POCO.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="2f0cd-899">Al igual que ocurre con el enlace de un objeto simple, solo se enlazan las propiedades públicas de lectura o escritura.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="2f0cd-900">El ejemplo contiene un modelo `TvShow` cuyo gráfico de objetos incluye las clases `Metadata` y `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="2f0cd-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="2f0cd-901">La aplicación de ejemplo tiene un archivo *tvshow.xml* que contiene los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="2f0cd-902">Configuración está enlazada a todo el gráfico de objetos `TvShow`con el método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="2f0cd-903">La instancia enlazada se asigna a una propiedad para la representación:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="2f0cd-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2f0cd-905">`Get<T>` es más conveniente que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="2f0cd-906">En el código siguiente se muestra cómo usar `Get<T>` con el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2f0cd-907">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="2f0cd-907">Bind an array to a class</span></span>

<span data-ttu-id="2f0cd-908">*La aplicación de ejemplo muestra los conceptos que se explican en esta sección.*</span><span class="sxs-lookup"><span data-stu-id="2f0cd-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="2f0cd-909"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2f0cd-910">Cualquier formato de matriz que expone un segmento de clave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) es capaz de enlazar una matriz a una matriz de clase POCO.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="2f0cd-911">El enlace se proporciona por convención.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-911">Binding is provided by convention.</span></span> <span data-ttu-id="2f0cd-912">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="2f0cd-913">**Procesamiento de matriz en memoria**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-913">**In-memory array processing**</span></span>

<span data-ttu-id="2f0cd-914">Considere los valores y las claves de configuración que se muestran en esta tabla.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="2f0cd-915">Key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-915">Key</span></span>             | <span data-ttu-id="2f0cd-916">Valor</span><span class="sxs-lookup"><span data-stu-id="2f0cd-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2f0cd-917">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-917">array:entries:0</span></span> | <span data-ttu-id="2f0cd-918">value0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-918">value0</span></span> |
| <span data-ttu-id="2f0cd-919">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-919">array:entries:1</span></span> | <span data-ttu-id="2f0cd-920">value1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-920">value1</span></span> |
| <span data-ttu-id="2f0cd-921">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-921">array:entries:2</span></span> | <span data-ttu-id="2f0cd-922">value2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-922">value2</span></span> |
| <span data-ttu-id="2f0cd-923">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-923">array:entries:4</span></span> | <span data-ttu-id="2f0cd-924">value4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-924">value4</span></span> |
| <span data-ttu-id="2f0cd-925">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="2f0cd-925">array:entries:5</span></span> | <span data-ttu-id="2f0cd-926">value5</span><span class="sxs-lookup"><span data-stu-id="2f0cd-926">value5</span></span> |

<span data-ttu-id="2f0cd-927">Estas claves y valores se cargan en la aplicación de ejemplo a través del proveedor de configuración de memoria:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="2f0cd-928">La matriz omite un valor de índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="2f0cd-929">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en los objetos enlazados, lo que queda claro cuando se muestra el resultado de enlazar esta matriz a un objeto.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="2f0cd-930">En la aplicación de ejemplo, hay disponible una clase POCO para almacenar los datos de configuración enlazados:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="2f0cd-931">Los datos de configuración están enlazados al objeto:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="2f0cd-932">También se puede usar la sintaxis [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*), lo que genera un código más compacto:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="2f0cd-933">El objeto enlazado, una instancia de `ArrayExample`, recibe los datos de la matriz desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="2f0cd-934">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2f0cd-935">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2f0cd-936">0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-936">0</span></span>                            | <span data-ttu-id="2f0cd-937">value0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-937">value0</span></span>                       |
| <span data-ttu-id="2f0cd-938">1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-938">1</span></span>                            | <span data-ttu-id="2f0cd-939">value1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-939">value1</span></span>                       |
| <span data-ttu-id="2f0cd-940">2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-940">2</span></span>                            | <span data-ttu-id="2f0cd-941">value2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-941">value2</span></span>                       |
| <span data-ttu-id="2f0cd-942">3</span><span class="sxs-lookup"><span data-stu-id="2f0cd-942">3</span></span>                            | <span data-ttu-id="2f0cd-943">value4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-943">value4</span></span>                       |
| <span data-ttu-id="2f0cd-944">4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-944">4</span></span>                            | <span data-ttu-id="2f0cd-945">value5</span><span class="sxs-lookup"><span data-stu-id="2f0cd-945">value5</span></span>                       |

<span data-ttu-id="2f0cd-946">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2f0cd-947">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración solo se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2f0cd-948">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2f0cd-949">El elemento de configuración omitido para el índice &num;3 se puede proporcionar antes del enlace a la instancia `ArrayExample` por cualquier proveedor de configuración que genera el par clave-valor correcto en la configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="2f0cd-950">Si el ejemplo incluía un proveedor de configuración JSON adicional con el par clave-valor omitido, `ArrayExample.Entries` coincide con la matriz de configuración completa:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="2f0cd-951">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="2f0cd-952">En `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="2f0cd-953">El par clave-valor que se muestra en la tabla se carga en la configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="2f0cd-954">Clave</span><span class="sxs-lookup"><span data-stu-id="2f0cd-954">Key</span></span>             | <span data-ttu-id="2f0cd-955">Value</span><span class="sxs-lookup"><span data-stu-id="2f0cd-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2f0cd-956">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="2f0cd-956">array:entries:3</span></span> | <span data-ttu-id="2f0cd-957">value3</span><span class="sxs-lookup"><span data-stu-id="2f0cd-957">value3</span></span> |

<span data-ttu-id="2f0cd-958">Si la instancia de clase `ArrayExample` se enlaza después de que el proveedor de configuración JSON incluye la entrada para el índice &num;3, la matriz `ArrayExample.Entries` incluye el valor.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="2f0cd-959">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2f0cd-960">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2f0cd-961">0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-961">0</span></span>                            | <span data-ttu-id="2f0cd-962">value0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-962">value0</span></span>                       |
| <span data-ttu-id="2f0cd-963">1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-963">1</span></span>                            | <span data-ttu-id="2f0cd-964">value1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-964">value1</span></span>                       |
| <span data-ttu-id="2f0cd-965">2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-965">2</span></span>                            | <span data-ttu-id="2f0cd-966">value2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-966">value2</span></span>                       |
| <span data-ttu-id="2f0cd-967">3</span><span class="sxs-lookup"><span data-stu-id="2f0cd-967">3</span></span>                            | <span data-ttu-id="2f0cd-968">value3</span><span class="sxs-lookup"><span data-stu-id="2f0cd-968">value3</span></span>                       |
| <span data-ttu-id="2f0cd-969">4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-969">4</span></span>                            | <span data-ttu-id="2f0cd-970">value4</span><span class="sxs-lookup"><span data-stu-id="2f0cd-970">value4</span></span>                       |
| <span data-ttu-id="2f0cd-971">5</span><span class="sxs-lookup"><span data-stu-id="2f0cd-971">5</span></span>                            | <span data-ttu-id="2f0cd-972">value5</span><span class="sxs-lookup"><span data-stu-id="2f0cd-972">value5</span></span>                       |

<span data-ttu-id="2f0cd-973">**Procesamiento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="2f0cd-973">**JSON array processing**</span></span>

<span data-ttu-id="2f0cd-974">Si un archivo JSON contiene una matriz, se crean claves de configuración para los elementos de la matriz con un índice de sección basado en cero.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="2f0cd-975">En el siguiente archivo de configuración, `subsection` es una matriz:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="2f0cd-976">El proveedor de configuración JSON lee los datos de configuración en los siguientes pares clave-valor:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="2f0cd-977">Key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-977">Key</span></span>                     | <span data-ttu-id="2f0cd-978">Value</span><span class="sxs-lookup"><span data-stu-id="2f0cd-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="2f0cd-979">json_array:key</span><span class="sxs-lookup"><span data-stu-id="2f0cd-979">json_array:key</span></span>          | <span data-ttu-id="2f0cd-980">valueA</span><span class="sxs-lookup"><span data-stu-id="2f0cd-980">valueA</span></span> |
| <span data-ttu-id="2f0cd-981">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-981">json_array:subsection:0</span></span> | <span data-ttu-id="2f0cd-982">valueB</span><span class="sxs-lookup"><span data-stu-id="2f0cd-982">valueB</span></span> |
| <span data-ttu-id="2f0cd-983">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-983">json_array:subsection:1</span></span> | <span data-ttu-id="2f0cd-984">valueC</span><span class="sxs-lookup"><span data-stu-id="2f0cd-984">valueC</span></span> |
| <span data-ttu-id="2f0cd-985">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-985">json_array:subsection:2</span></span> | <span data-ttu-id="2f0cd-986">valueD</span><span class="sxs-lookup"><span data-stu-id="2f0cd-986">valueD</span></span> |

<span data-ttu-id="2f0cd-987">En la aplicación de ejemplo, la clase POCO siguiente está disponible para enlazar los pares clave-valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="2f0cd-988">Después del enlace, `JsonArrayExample.Key` contiene el valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="2f0cd-989">Los valores de la subsección se almacenan en la propiedad de la matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="2f0cd-990">Índice de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="2f0cd-991">Valor de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2f0cd-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="2f0cd-992">0</span><span class="sxs-lookup"><span data-stu-id="2f0cd-992">0</span></span>                                   | <span data-ttu-id="2f0cd-993">valueB</span><span class="sxs-lookup"><span data-stu-id="2f0cd-993">valueB</span></span>                              |
| <span data-ttu-id="2f0cd-994">1</span><span class="sxs-lookup"><span data-stu-id="2f0cd-994">1</span></span>                                   | <span data-ttu-id="2f0cd-995">valueC</span><span class="sxs-lookup"><span data-stu-id="2f0cd-995">valueC</span></span>                              |
| <span data-ttu-id="2f0cd-996">2</span><span class="sxs-lookup"><span data-stu-id="2f0cd-996">2</span></span>                                   | <span data-ttu-id="2f0cd-997">valueD</span><span class="sxs-lookup"><span data-stu-id="2f0cd-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="2f0cd-998">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="2f0cd-998">Custom configuration provider</span></span>

<span data-ttu-id="2f0cd-999">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2f0cd-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2f0cd-1000">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2f0cd-1001">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2f0cd-1002">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2f0cd-1003">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2f0cd-1004">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2f0cd-1005">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2f0cd-1006">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2f0cd-1007">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2f0cd-1008">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2f0cd-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2f0cd-1010">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2f0cd-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2f0cd-1012">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2f0cd-1013">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="2f0cd-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2f0cd-1015">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2f0cd-1016">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2f0cd-1017">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="2f0cd-1018">Acceso a la configuración durante el inicio</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1018">Access configuration during startup</span></span>

<span data-ttu-id="2f0cd-1019">Inserte `IConfiguration` en el constructor `Startup` para acceder a los valores de configuración en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f0cd-1020">Para acceder a la configuración en `Startup.Configure`, inserte `IConfiguration` directamente en el método o use la instancia desde el constructor:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="2f0cd-1021">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="2f0cd-1022">Acceso a la configuración en una página de Razor Pages o en una vista de MVC</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="2f0cd-1023">Para acceder a los valores de configuración en una página de Razor Pages o una vista de MVC, agregue una [directiva using](xref:mvc/views/razor#using) ([Referencia de C#: directiva using](/dotnet/csharp/language-reference/keywords/using-directive)) para el [espacio de nombres Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inyecte <xref:Microsoft.Extensions.Configuration.IConfiguration> en la página o en la vista.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="2f0cd-1024">En una página de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="2f0cd-1025">En una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2f0cd-1026">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="2f0cd-1027">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2f0cd-1028">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f0cd-1029">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2f0cd-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
