---
title: Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core
author: rick-anderson
description: Aprenda a almacenar y recuperar información confidencial durante el desarrollo de una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486205"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="069a6-103">Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="069a6-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="069a6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="069a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="069a6-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="069a6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="069a6-106">En este documento se explica cómo administrar la información confidencial de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="069a6-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="069a6-107">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="069a6-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="069a6-108">Los secretos de producción no deben usarse para el desarrollo o las pruebas.</span><span class="sxs-lookup"><span data-stu-id="069a6-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="069a6-109">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="069a6-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="069a6-110">En su lugar, se debe tener acceso a los secretos de producción a través de un medio controlado como variables de entorno o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="069a6-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="069a6-111">Puede almacenar y proteger sus secretos de producción y pruebas de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="069a6-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="069a6-112">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="069a6-112">Environment variables</span></span>

<span data-ttu-id="069a6-113">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="069a6-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="069a6-114">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.</span><span class="sxs-lookup"><span data-stu-id="069a6-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="069a6-115">Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="069a6-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="069a6-116">Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="069a6-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="069a6-117">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="069a6-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="069a6-118">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="069a6-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="069a6-119">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="069a6-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="069a6-120">Normalmente, las variables de entorno se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="069a6-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="069a6-121">Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="069a6-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="069a6-122">Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="069a6-123">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-123">Secret Manager</span></span>

<span data-ttu-id="069a6-124">La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="069a6-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="069a6-125">En este contexto, una parte de la información confidencial es un secreto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="069a6-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="069a6-126">Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="069a6-127">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="069a6-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="069a6-128">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="069a6-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="069a6-129">La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="069a6-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="069a6-130">Solo es para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="069a6-130">It's for development purposes only.</span></span> <span data-ttu-id="069a6-131">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="069a6-132">Cómo funciona la herramienta Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="069a6-133">La herramienta Administrador de secretos oculta los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="069a6-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="069a6-134">Puede usar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="069a6-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="069a6-135">Los valores se almacenan en un archivo JSON en la carpeta de Perfil de usuario del equipo local:</span><span class="sxs-lookup"><span data-stu-id="069a6-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="069a6-136">Windows</span><span class="sxs-lookup"><span data-stu-id="069a6-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="069a6-137">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="069a6-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="069a6-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="069a6-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="069a6-139">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="069a6-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="069a6-140">En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="069a6-141">No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="069a6-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="069a6-142">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="069a6-142">These implementation details may change.</span></span> <span data-ttu-id="069a6-143">Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="069a6-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="069a6-144">Habilitar el almacenamiento de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-144">Enable secret storage</span></span>

<span data-ttu-id="069a6-145">La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="069a6-146">La herramienta Administrador de secretos incluye un `init` comando en SDK de .net Core 3.0.100 o posterior.</span><span class="sxs-lookup"><span data-stu-id="069a6-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="069a6-147">Para usar los secretos de usuario, ejecute el siguiente comando en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="069a6-148">El comando anterior agrega un `UserSecretsId` elemento dentro `PropertyGroup` de un del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="069a6-149">De forma predeterminada, el texto interno de `UserSecretsId` es un GUID.</span><span class="sxs-lookup"><span data-stu-id="069a6-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="069a6-150">El texto interno es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="069a6-151">En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="069a6-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="069a6-152">Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="069a6-153">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-153">Set a secret</span></span>

<span data-ttu-id="069a6-154">Defina un secreto de la aplicación que conste de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="069a6-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="069a6-155">El secreto está asociado con el valor del proyecto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="069a6-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="069a6-156">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="069a6-157">En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="069a6-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="069a6-158">La herramienta Administrador de secretos también se puede usar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="069a6-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="069a6-159">Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="069a6-160">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="069a6-161">Simplificación de la estructura de JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="069a6-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="069a6-162">El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="069a6-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="069a6-163">Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="069a6-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="069a6-164">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="069a6-165">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="069a6-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="069a6-166">Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="069a6-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="069a6-167">El archivo modificado es similar al siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="069a6-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="069a6-168">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-168">Set multiple secrets</span></span>

<span data-ttu-id="069a6-169">Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando.</span><span class="sxs-lookup"><span data-stu-id="069a6-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="069a6-170">En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="069a6-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="069a6-171">Windows</span><span class="sxs-lookup"><span data-stu-id="069a6-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="069a6-172">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="069a6-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="069a6-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="069a6-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="069a6-174">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="069a6-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="069a6-175">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-175">Access a secret</span></span>

<span data-ttu-id="069a6-176">Para obtener acceso a un secreto, complete los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="069a6-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="069a6-177">Registrar el origen de configuración de secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="069a6-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="069a6-178">Leer el secreto a través de la API de configuración</span><span class="sxs-lookup"><span data-stu-id="069a6-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="069a6-179">Registrar el origen de configuración de secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="069a6-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="069a6-180">El proveedor de [configuración](/dotnet/core/extensions/configuration-providers) de secretos de usuario registra el origen de configuración adecuado con la [API de configuración](xref:fundamentals/configuration/index)de .net.</span><span class="sxs-lookup"><span data-stu-id="069a6-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="069a6-181">El origen de configuración de secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="069a6-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="069a6-182">`CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> es <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="069a6-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="069a6-183">Cuando `CreateDefaultBuilder` no se llama a, agregue el origen de configuración de secretos de usuario explícitamente llamando a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="069a6-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="069a6-184">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="069a6-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="069a6-185">Leer el secreto a través de la API de configuración</span><span class="sxs-lookup"><span data-stu-id="069a6-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="069a6-186">Si el origen de configuración de secretos de usuario está registrado, la API de configuración de .NET puede leer los secretos.</span><span class="sxs-lookup"><span data-stu-id="069a6-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="069a6-187">La [inserción de constructores](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) se puede usar para obtener acceso a la API de configuración de .net.</span><span class="sxs-lookup"><span data-stu-id="069a6-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="069a6-188">Tenga en cuenta los siguientes ejemplos de lectura de la `Movies:ServiceApiKey` clave:</span><span class="sxs-lookup"><span data-stu-id="069a6-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="069a6-189">**Clase de Inicio:**</span><span class="sxs-lookup"><span data-stu-id="069a6-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="069a6-190">**Razor Modelo de página de páginas:**</span><span class="sxs-lookup"><span data-stu-id="069a6-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="069a6-191">Para obtener más información, consulte [configuración de acceso en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup) y [configuración de acceso en Razor páginas](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="069a6-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="069a6-192">Asignación de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="069a6-192">Map secrets to a POCO</span></span>

<span data-ttu-id="069a6-193">La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="069a6-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-194">Para asignar los secretos anteriores a un POCO, use la característica de enlace de [gráficos de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API de configuración de .net.</span><span class="sxs-lookup"><span data-stu-id="069a6-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="069a6-195">El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:</span><span class="sxs-lookup"><span data-stu-id="069a6-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="069a6-196">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="069a6-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="069a6-197">Reemplazo de cadenas con secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-197">String replacement with secrets</span></span>

<span data-ttu-id="069a6-198">Almacenar las contraseñas en texto sin formato no es seguro.</span><span class="sxs-lookup"><span data-stu-id="069a6-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="069a6-199">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="069a6-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="069a6-200">Un enfoque más seguro consiste en almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="069a6-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="069a6-201">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="069a6-202">Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="069a6-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="069a6-203">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="069a6-204">El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="069a6-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="069a6-205">Enumeración de los secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-206">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="069a6-207">Se mostrará la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="069a6-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="069a6-208">En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="069a6-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="069a6-209">Quitar un único secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-210">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="069a6-211">Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:</span><span class="sxs-lookup"><span data-stu-id="069a6-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="069a6-212">`dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="069a6-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="069a6-213">Quitar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-214">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="069a6-215">Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="069a6-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="069a6-216">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="069a6-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="069a6-217">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="069a6-217">Additional resources</span></span>

* <span data-ttu-id="069a6-218">Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a los secretos de los usuarios desde IIS.</span><span class="sxs-lookup"><span data-stu-id="069a6-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="069a6-219">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="069a6-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="069a6-220">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="069a6-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="069a6-221">En este documento se explica cómo administrar la información confidencial de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="069a6-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="069a6-222">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="069a6-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="069a6-223">Los secretos de producción no deben usarse para el desarrollo o las pruebas.</span><span class="sxs-lookup"><span data-stu-id="069a6-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="069a6-224">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="069a6-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="069a6-225">En su lugar, se debe tener acceso a los secretos de producción a través de un medio controlado como variables de entorno o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="069a6-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="069a6-226">Puede almacenar y proteger sus secretos de producción y pruebas de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="069a6-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="069a6-227">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="069a6-227">Environment variables</span></span>

<span data-ttu-id="069a6-228">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="069a6-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="069a6-229">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.</span><span class="sxs-lookup"><span data-stu-id="069a6-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="069a6-230">Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="069a6-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="069a6-231">Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="069a6-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="069a6-232">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="069a6-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="069a6-233">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="069a6-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="069a6-234">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="069a6-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="069a6-235">Normalmente, las variables de entorno se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="069a6-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="069a6-236">Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="069a6-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="069a6-237">Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="069a6-238">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-238">Secret Manager</span></span>

<span data-ttu-id="069a6-239">La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="069a6-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="069a6-240">En este contexto, una parte de la información confidencial es un secreto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="069a6-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="069a6-241">Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="069a6-242">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="069a6-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="069a6-243">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="069a6-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="069a6-244">La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="069a6-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="069a6-245">Solo es para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="069a6-245">It's for development purposes only.</span></span> <span data-ttu-id="069a6-246">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="069a6-247">Cómo funciona la herramienta Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="069a6-248">La herramienta Administrador de secretos oculta los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="069a6-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="069a6-249">Puede usar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="069a6-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="069a6-250">Los valores se almacenan en un archivo JSON en la carpeta de Perfil de usuario del equipo local:</span><span class="sxs-lookup"><span data-stu-id="069a6-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="069a6-251">Windows</span><span class="sxs-lookup"><span data-stu-id="069a6-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="069a6-252">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="069a6-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="069a6-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="069a6-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="069a6-254">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="069a6-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="069a6-255">En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="069a6-256">No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="069a6-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="069a6-257">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="069a6-257">These implementation details may change.</span></span> <span data-ttu-id="069a6-258">Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="069a6-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="069a6-259">Habilitar el almacenamiento de secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-259">Enable secret storage</span></span>

<span data-ttu-id="069a6-260">La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="069a6-261">Para usar secretos de usuario, defina un `UserSecretsId` elemento dentro `PropertyGroup` de un del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="069a6-262">El texto interno de `UserSecretsId` es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="069a6-263">Normalmente, los desarrolladores generan un GUID para `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="069a6-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="069a6-264">En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="069a6-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="069a6-265">Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="069a6-266">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-266">Set a secret</span></span>

<span data-ttu-id="069a6-267">Defina un secreto de la aplicación que conste de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="069a6-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="069a6-268">El secreto está asociado con el valor del proyecto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="069a6-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="069a6-269">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="069a6-270">En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="069a6-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="069a6-271">La herramienta Administrador de secretos también se puede usar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="069a6-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="069a6-272">Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="069a6-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="069a6-273">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="069a6-274">Simplificación de la estructura de JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="069a6-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="069a6-275">El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="069a6-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="069a6-276">Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="069a6-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="069a6-277">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="069a6-278">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="069a6-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="069a6-279">Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="069a6-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="069a6-280">El archivo modificado es similar al siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="069a6-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="069a6-281">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-281">Set multiple secrets</span></span>

<span data-ttu-id="069a6-282">Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando.</span><span class="sxs-lookup"><span data-stu-id="069a6-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="069a6-283">En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="069a6-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="069a6-284">Windows</span><span class="sxs-lookup"><span data-stu-id="069a6-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="069a6-285">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="069a6-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="069a6-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="069a6-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="069a6-287">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="069a6-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="069a6-288">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-288">Access a secret</span></span>

<span data-ttu-id="069a6-289">La [API de configuración](xref:fundamentals/configuration/index) proporciona acceso a los secretos del usuario.</span><span class="sxs-lookup"><span data-stu-id="069a6-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="069a6-290">Si el proyecto tiene como destino .NET Framework, instale el [Microsoft.Extensions.Configprimario. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Paquete NuGet de UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="069a6-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="069a6-291">En ASP.NET Core 2,0 o posterior, el origen de configuración de los secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="069a6-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="069a6-292">`CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> es <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="069a6-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="069a6-293">Cuando `CreateDefaultBuilder` no se llama a, se agrega explícitamente el origen de configuración de secretos de usuario mediante una llamada a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> en el `Startup` constructor.</span><span class="sxs-lookup"><span data-stu-id="069a6-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="069a6-294">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="069a6-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="069a6-295">Los secretos de usuario se pueden recuperar a través de la API de configuración de .NET:</span><span class="sxs-lookup"><span data-stu-id="069a6-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="069a6-296">Asignación de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="069a6-296">Map secrets to a POCO</span></span>

<span data-ttu-id="069a6-297">La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="069a6-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-298">Para asignar los secretos anteriores a un POCO, use la característica de enlace de [gráficos de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API de configuración de .net.</span><span class="sxs-lookup"><span data-stu-id="069a6-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="069a6-299">El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:</span><span class="sxs-lookup"><span data-stu-id="069a6-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="069a6-300">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="069a6-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="069a6-301">Reemplazo de cadenas con secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-301">String replacement with secrets</span></span>

<span data-ttu-id="069a6-302">Almacenar las contraseñas en texto sin formato no es seguro.</span><span class="sxs-lookup"><span data-stu-id="069a6-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="069a6-303">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="069a6-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="069a6-304">Un enfoque más seguro consiste en almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="069a6-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="069a6-305">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="069a6-306">Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="069a6-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="069a6-307">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="069a6-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="069a6-308">El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="069a6-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="069a6-309">Enumeración de los secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-310">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="069a6-311">Se mostrará la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="069a6-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="069a6-312">En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="069a6-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="069a6-313">Quitar un único secreto</span><span class="sxs-lookup"><span data-stu-id="069a6-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-314">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="069a6-315">Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:</span><span class="sxs-lookup"><span data-stu-id="069a6-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="069a6-316">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="069a6-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="069a6-317">Quitar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="069a6-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="069a6-318">Ejecute el siguiente comando desde el directorio en el que existe el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="069a6-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="069a6-319">Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="069a6-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="069a6-320">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="069a6-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="069a6-321">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="069a6-321">Additional resources</span></span>

* <span data-ttu-id="069a6-322">Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a los secretos de los usuarios desde IIS.</span><span class="sxs-lookup"><span data-stu-id="069a6-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end