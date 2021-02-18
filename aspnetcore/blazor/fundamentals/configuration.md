---
title: Configuración de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar aplicaciones Blazor, incluidas opciones de configuración de la aplicación, la autenticación y el registro.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 48d78f40e9254bac182ffbc534550157664bcc5b
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106939"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="206c5-103">Configuración de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="206c5-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="206c5-104">Este tema se aplica a Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="206c5-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="206c5-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="206c5-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="206c5-106">Blazor WebAssembly carga la configuración de los siguientes archivos de configuración de la aplicación de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="206c5-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="206c5-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="206c5-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="206c5-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, donde el marcador de posición `{ENVIRONMENT}` es el [entorno en tiempo de ejecución](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="206c5-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="206c5-109">Otros proveedores de configuración registrados por la aplicación también pueden proporcionar la configuración, pero no todos los proveedores o características de proveedor son adecuados para aplicaciones Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="206c5-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="206c5-110">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration): no se admite el proveedor para identidad administrada e identificador de aplicación (Id. de cliente) con escenarios de secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="206c5-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="206c5-111">No se recomienda usar el identificador de la aplicación con un secreto de cliente para ninguna aplicación ASP.NET Core, especialmente las aplicaciones Blazor WebAssembly, porque el secreto de cliente no se puede proteger en el lado cliente para acceder al servicio Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="206c5-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="206c5-112">[Proveedor de configuración de aplicaciones de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): el proveedor no es adecuado para las aplicaciones Blazor WebAssembly porque las aplicaciones Blazor WebAssembly no se ejecutan en un servidor de Azure.</span><span class="sxs-lookup"><span data-stu-id="206c5-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="206c5-113">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="206c5-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="206c5-114">**No almacene secretos de aplicación, credenciales ni otros datos confidenciales en la configuración de una aplicación Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="206c5-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="206c5-115">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="206c5-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="206c5-116">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="206c5-116">App settings configuration</span></span>

<span data-ttu-id="206c5-117">La configuración de los archivos de configuración de la aplicación se carga de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="206c5-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="206c5-118">En el siguiente ejemplo, un valor de configuración de la interfaz de usuario se almacena en un archivo de configuración de la aplicación y lo carga el marco Blazor automáticamente.</span><span class="sxs-lookup"><span data-stu-id="206c5-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="206c5-119">Un componente lee el valor.</span><span class="sxs-lookup"><span data-stu-id="206c5-119">The value is read by a component.</span></span>

<span data-ttu-id="206c5-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="206c5-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="206c5-121">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="206c5-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="206c5-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="206c5-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="206c5-123">Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="206c5-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="206c5-124">En el ejemplo siguiente se lee un archivo de configuración (`cars.json`) en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="206c5-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="206c5-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="206c5-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="206c5-126">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="206c5-127">En `Program.Main` de `Program.cs`, modifique el registro del servicio <xref:System.Net.Http.HttpClient> existente para usar el cliente para leer el archivo:</span><span class="sxs-lookup"><span data-stu-id="206c5-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a><span data-ttu-id="206c5-128">Origen de la configuración de la memoria</span><span class="sxs-lookup"><span data-stu-id="206c5-128">Memory Configuration Source</span></span>

<span data-ttu-id="206c5-129">En el ejemplo siguiente se usa un elemento <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> en `Program.Main` para proporcionar configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="206c5-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="206c5-130">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="206c5-131">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-131">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="206c5-132">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="206c5-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="206c5-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="206c5-133">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="206c5-134">Obtenga una sección de la configuración en el código de C# con <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="206c5-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="206c5-135">En el ejemplo siguiente se obtiene la sección `wheels` para la configuración del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="206c5-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="206c5-136">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="206c5-136">Authentication configuration</span></span>

<span data-ttu-id="206c5-137">Proporcione la configuración de autenticación en un archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="206c5-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="206c5-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="206c5-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="206c5-139">Cargue la configuración de un proveedor de Identity con <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="206c5-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="206c5-140">En el ejemplo siguiente se carga la configuración de un [proveedor de OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="206c5-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="206c5-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="206c5-142">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="206c5-142">Logging configuration</span></span>

<span data-ttu-id="206c5-143">Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) al archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="206c5-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="206c5-144">En el archivo de configuración de la aplicación, proporcione la configuración de registro.</span><span class="sxs-lookup"><span data-stu-id="206c5-144">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="206c5-145">La configuración de registro se carga en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="206c5-145">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="206c5-146">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="206c5-146">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="206c5-147">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Logging?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-147">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="206c5-148">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-148">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="206c5-149">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="206c5-149">Host builder configuration</span></span>

<span data-ttu-id="206c5-150">Lea configuración del generador de host desde <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="206c5-150">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="206c5-151">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="206c5-151">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="206c5-152">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="206c5-152">Cached configuration</span></span>

<span data-ttu-id="206c5-153">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="206c5-153">Configuration files are cached for offline use.</span></span> <span data-ttu-id="206c5-154">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="206c5-154">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="206c5-155">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="206c5-155">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="206c5-156">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="206c5-156">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="206c5-157">Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="206c5-157">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="206c5-158">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="206c5-158">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
