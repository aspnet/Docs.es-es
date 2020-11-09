---
title: Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core
author: rick-anderson
description: Aprenda a almacenar y recuperar información confidencial como secretos de la aplicación durante el desarrollo de una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056873"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="e900c-103">Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e900c-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e900c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e900c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e900c-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e900c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e900c-106">En este documento se explican técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e900c-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e900c-107">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="e900c-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e900c-108">Los secretos de producción no deben usarse para el desarrollo o las pruebas.</span><span class="sxs-lookup"><span data-stu-id="e900c-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e900c-109">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e900c-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e900c-110">En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e900c-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e900c-111">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="e900c-111">Environment variables</span></span>

<span data-ttu-id="e900c-112">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="e900c-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e900c-113">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.</span><span class="sxs-lookup"><span data-stu-id="e900c-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e900c-114">Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="e900c-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e900c-115">Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e900c-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e900c-116">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="e900c-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e900c-117">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="e900c-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e900c-118">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="e900c-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e900c-119">Normalmente, las variables de entorno se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="e900c-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e900c-120">Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="e900c-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e900c-121">Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e900c-122">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-122">Secret Manager</span></span>

<span data-ttu-id="e900c-123">La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e900c-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e900c-124">En este contexto, una parte de la información confidencial es un secreto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e900c-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e900c-125">Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e900c-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e900c-126">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="e900c-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e900c-127">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="e900c-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e900c-128">La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="e900c-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e900c-129">Solo es para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e900c-129">It's for development purposes only.</span></span> <span data-ttu-id="e900c-130">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e900c-131">Cómo funciona la herramienta Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="e900c-132">La herramienta Administrador de secretos abstrae los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="e900c-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e900c-133">Puede usar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="e900c-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e900c-134">Los valores se almacenan en un archivo de configuración JSON en una carpeta de Perfil de usuario protegida por el sistema en el equipo local:</span><span class="sxs-lookup"><span data-stu-id="e900c-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e900c-135">Windows</span><span class="sxs-lookup"><span data-stu-id="e900c-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="e900c-136">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="e900c-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e900c-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e900c-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e900c-138">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="e900c-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e900c-139">En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="e900c-140">No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="e900c-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e900c-141">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="e900c-141">These implementation details may change.</span></span> <span data-ttu-id="e900c-142">Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="e900c-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e900c-143">Habilitar el almacenamiento de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-143">Enable secret storage</span></span>

<span data-ttu-id="e900c-144">La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e900c-145">La herramienta Administrador de secretos incluye un `init` comando en SDK de .net Core 3.0.100 o posterior.</span><span class="sxs-lookup"><span data-stu-id="e900c-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="e900c-146">Para usar los secretos de usuario, ejecute el siguiente comando en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="e900c-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="e900c-147">El comando anterior agrega un `UserSecretsId` elemento dentro `PropertyGroup` del archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="e900c-148">De forma predeterminada, el texto interno de `UserSecretsId` es un GUID.</span><span class="sxs-lookup"><span data-stu-id="e900c-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="e900c-149">El texto interno es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e900c-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="e900c-150">En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="e900c-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e900c-151">Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e900c-152">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-152">Set a secret</span></span>

<span data-ttu-id="e900c-153">Defina un secreto de la aplicación que conste de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="e900c-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e900c-154">El secreto está asociado con el valor del proyecto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e900c-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e900c-155">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e900c-156">En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="e900c-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e900c-157">La herramienta Administrador de secretos también se puede usar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="e900c-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e900c-158">Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="e900c-159">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e900c-160">Simplificación de la estructura de JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e900c-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e900c-161">El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e900c-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e900c-162">Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="e900c-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e900c-163">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e900c-164">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e900c-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e900c-165">Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="e900c-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e900c-166">El archivo modificado es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e900c-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e900c-167">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-167">Set multiple secrets</span></span>

<span data-ttu-id="e900c-168">Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando.</span><span class="sxs-lookup"><span data-stu-id="e900c-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e900c-169">En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="e900c-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e900c-170">Windows</span><span class="sxs-lookup"><span data-stu-id="e900c-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="e900c-171">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e900c-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e900c-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e900c-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e900c-173">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e900c-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e900c-174">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-174">Access a secret</span></span>

<span data-ttu-id="e900c-175">La [API de configuración de ASP.net Core](xref:fundamentals/configuration/index) proporciona acceso a los secretos del administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="e900c-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="e900c-176">El origen de configuración de secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> a para inicializar una nueva instancia del host con los valores predeterminados preconfigurados.</span><span class="sxs-lookup"><span data-stu-id="e900c-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="e900c-177">`CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> es <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e900c-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="e900c-178">Cuando `CreateDefaultBuilder` no se llama a, agregue el origen de configuración de secretos de usuario explícitamente mediante una llamada a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="e900c-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="e900c-179">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e900c-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="e900c-180">Los secretos de usuario se pueden recuperar a través de la `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="e900c-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e900c-181">Asignación de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="e900c-181">Map secrets to a POCO</span></span>

<span data-ttu-id="e900c-182">La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e900c-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-183">Para asignar los secretos anteriores a un POCO, use la `Configuration` característica de [enlace del gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API.</span><span class="sxs-lookup"><span data-stu-id="e900c-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e900c-184">El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:</span><span class="sxs-lookup"><span data-stu-id="e900c-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e900c-185">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e900c-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e900c-186">Reemplazo de cadenas con secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-186">String replacement with secrets</span></span>

<span data-ttu-id="e900c-187">Almacenar las contraseñas en texto sin formato no es seguro.</span><span class="sxs-lookup"><span data-stu-id="e900c-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e900c-188">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="e900c-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e900c-189">Un enfoque más seguro consiste en almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="e900c-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e900c-190">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e900c-191">Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e900c-191">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="e900c-192">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e900c-193">El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="e900c-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e900c-194">Enumeración de los secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-195">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e900c-196">Se mostrará la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="e900c-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e900c-197">En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js* .</span><span class="sxs-lookup"><span data-stu-id="e900c-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e900c-198">Quitar un único secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-199">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e900c-200">Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:</span><span class="sxs-lookup"><span data-stu-id="e900c-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e900c-201">`dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="e900c-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e900c-202">Quitar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-203">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e900c-204">Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="e900c-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e900c-205">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="e900c-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e900c-206">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e900c-206">Additional resources</span></span>

* <span data-ttu-id="e900c-207">Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a Secret Manager desde IIS.</span><span class="sxs-lookup"><span data-stu-id="e900c-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e900c-208">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e900c-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e900c-209">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e900c-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e900c-210">En este documento se explican técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e900c-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e900c-211">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="e900c-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e900c-212">Los secretos de producción no deben usarse para el desarrollo o las pruebas.</span><span class="sxs-lookup"><span data-stu-id="e900c-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e900c-213">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e900c-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e900c-214">En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e900c-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e900c-215">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="e900c-215">Environment variables</span></span>

<span data-ttu-id="e900c-216">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="e900c-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e900c-217">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.</span><span class="sxs-lookup"><span data-stu-id="e900c-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e900c-218">Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="e900c-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e900c-219">Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e900c-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e900c-220">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="e900c-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e900c-221">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="e900c-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e900c-222">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="e900c-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e900c-223">Normalmente, las variables de entorno se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="e900c-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e900c-224">Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="e900c-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e900c-225">Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e900c-226">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-226">Secret Manager</span></span>

<span data-ttu-id="e900c-227">La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e900c-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e900c-228">En este contexto, una parte de la información confidencial es un secreto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e900c-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e900c-229">Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e900c-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e900c-230">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="e900c-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e900c-231">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="e900c-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e900c-232">La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="e900c-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e900c-233">Solo es para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e900c-233">It's for development purposes only.</span></span> <span data-ttu-id="e900c-234">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e900c-235">Cómo funciona la herramienta Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="e900c-236">La herramienta Administrador de secretos abstrae los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="e900c-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e900c-237">Puede usar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="e900c-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e900c-238">Los valores se almacenan en un archivo de configuración JSON en una carpeta de Perfil de usuario protegida por el sistema en el equipo local:</span><span class="sxs-lookup"><span data-stu-id="e900c-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e900c-239">Windows</span><span class="sxs-lookup"><span data-stu-id="e900c-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="e900c-240">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="e900c-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e900c-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e900c-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e900c-242">Ruta de acceso del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="e900c-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e900c-243">En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="e900c-244">No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="e900c-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e900c-245">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="e900c-245">These implementation details may change.</span></span> <span data-ttu-id="e900c-246">Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="e900c-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e900c-247">Habilitar el almacenamiento de secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-247">Enable secret storage</span></span>

<span data-ttu-id="e900c-248">La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="e900c-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e900c-249">Para usar secretos de usuario, defina un `UserSecretsId` elemento dentro `PropertyGroup` del archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="e900c-250">El texto interno de `UserSecretsId` es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e900c-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="e900c-251">Normalmente, los desarrolladores generan un GUID para `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e900c-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="e900c-252">En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="e900c-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e900c-253">Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e900c-254">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-254">Set a secret</span></span>

<span data-ttu-id="e900c-255">Defina un secreto de la aplicación que conste de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="e900c-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e900c-256">El secreto está asociado con el valor del proyecto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e900c-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e900c-257">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e900c-258">En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="e900c-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e900c-259">La herramienta Administrador de secretos también se puede usar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="e900c-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e900c-260">Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e900c-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="e900c-261">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e900c-262">Simplificación de la estructura de JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e900c-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e900c-263">El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e900c-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e900c-264">Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="e900c-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e900c-265">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e900c-266">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e900c-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e900c-267">Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="e900c-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e900c-268">El archivo modificado es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e900c-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e900c-269">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-269">Set multiple secrets</span></span>

<span data-ttu-id="e900c-270">Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando.</span><span class="sxs-lookup"><span data-stu-id="e900c-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e900c-271">En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="e900c-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e900c-272">Windows</span><span class="sxs-lookup"><span data-stu-id="e900c-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="e900c-273">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e900c-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e900c-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e900c-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e900c-275">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e900c-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e900c-276">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-276">Access a secret</span></span>

<span data-ttu-id="e900c-277">La [API de configuración de ASP.net Core](xref:fundamentals/configuration/index) proporciona acceso a los secretos del administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="e900c-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="e900c-278">Si el proyecto tiene como destino .NET Framework, instale el [Microsoft.Extensions.Configprimario. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Paquete NuGet de UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="e900c-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="e900c-279">En ASP.NET Core 2,0 o posterior, el origen de configuración de los secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> a para inicializar una nueva instancia del host con los valores predeterminados preconfigurados.</span><span class="sxs-lookup"><span data-stu-id="e900c-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="e900c-280">`CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> es <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e900c-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="e900c-281">Cuando `CreateDefaultBuilder` no se llama a, se agrega explícitamente el origen de configuración de secretos de usuario mediante una llamada a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> en el `Startup` constructor.</span><span class="sxs-lookup"><span data-stu-id="e900c-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="e900c-282">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e900c-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="e900c-283">Los secretos de usuario se pueden recuperar a través de la `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="e900c-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e900c-284">Asignación de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="e900c-284">Map secrets to a POCO</span></span>

<span data-ttu-id="e900c-285">La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e900c-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-286">Para asignar los secretos anteriores a un POCO, use la `Configuration` característica de [enlace del gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API.</span><span class="sxs-lookup"><span data-stu-id="e900c-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e900c-287">El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:</span><span class="sxs-lookup"><span data-stu-id="e900c-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e900c-288">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e900c-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e900c-289">Reemplazo de cadenas con secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-289">String replacement with secrets</span></span>

<span data-ttu-id="e900c-290">Almacenar las contraseñas en texto sin formato no es seguro.</span><span class="sxs-lookup"><span data-stu-id="e900c-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e900c-291">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="e900c-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e900c-292">Un enfoque más seguro consiste en almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="e900c-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e900c-293">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e900c-294">Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e900c-294">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="e900c-295">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e900c-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e900c-296">El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="e900c-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e900c-297">Enumeración de los secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-298">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e900c-299">Se mostrará la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="e900c-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e900c-300">En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js* .</span><span class="sxs-lookup"><span data-stu-id="e900c-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e900c-301">Quitar un único secreto</span><span class="sxs-lookup"><span data-stu-id="e900c-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-302">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e900c-303">Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:</span><span class="sxs-lookup"><span data-stu-id="e900c-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e900c-304">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="e900c-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e900c-305">Quitar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="e900c-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e900c-306">Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e900c-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e900c-307">Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="e900c-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e900c-308">Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="e900c-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e900c-309">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e900c-309">Additional resources</span></span>

* <span data-ttu-id="e900c-310">Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a Secret Manager desde IIS.</span><span class="sxs-lookup"><span data-stu-id="e900c-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end