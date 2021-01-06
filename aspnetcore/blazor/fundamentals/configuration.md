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
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485972"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="44fc3-103">Configuración de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44fc3-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="44fc3-104">Este tema se aplica a Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="44fc3-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="44fc3-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="44fc3-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="44fc3-106">Blazor WebAssembly carga la configuración de los siguientes archivos de configuración de la aplicación de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="44fc3-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="44fc3-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="44fc3-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="44fc3-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, donde el marcador de posición `{ENVIRONMENT}` es el [entorno en tiempo de ejecución](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44fc3-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="44fc3-109">Otros proveedores de configuración registrados por la aplicación también pueden proporcionar la configuración, pero no todos los proveedores o características de proveedor son adecuados para aplicaciones Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="44fc3-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="44fc3-110">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration): no se admite el proveedor para identidad administrada e identificador de aplicación (Id. de cliente) con escenarios de secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="44fc3-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="44fc3-111">No se recomienda usar el identificador de la aplicación con un secreto de cliente para ninguna aplicación ASP.NET Core, especialmente las aplicaciones Blazor WebAssembly, porque el secreto de cliente no se puede proteger en el lado cliente para acceder al servicio Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="44fc3-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="44fc3-112">[Proveedor de configuración de aplicaciones de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): el proveedor no es adecuado para las aplicaciones Blazor WebAssembly porque las aplicaciones Blazor WebAssembly no se ejecutan en un servidor de Azure.</span><span class="sxs-lookup"><span data-stu-id="44fc3-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="44fc3-113">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="44fc3-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="44fc3-114">**No almacene secretos de aplicación, credenciales ni otros datos confidenciales en la configuración de una aplicación Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="44fc3-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="44fc3-115">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="44fc3-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="44fc3-116">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="44fc3-116">App settings configuration</span></span>

<span data-ttu-id="44fc3-117">La configuración de los archivos de configuración de la aplicación se carga de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="44fc3-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="44fc3-118">En el siguiente ejemplo, un valor de configuración de la interfaz de usuario se almacena en un archivo de configuración de la aplicación y lo carga el marco Blazor automáticamente.</span><span class="sxs-lookup"><span data-stu-id="44fc3-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="44fc3-119">Un componente lee el valor.</span><span class="sxs-lookup"><span data-stu-id="44fc3-119">The value is read by a component.</span></span>

<span data-ttu-id="44fc3-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="44fc3-121">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="44fc3-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="44fc3-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="44fc3-123">Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="44fc3-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="44fc3-124">En el ejemplo siguiente se lee un archivo de configuración (`cars.json`) en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44fc3-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="44fc3-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="44fc3-126">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="44fc3-127">En `Program.Main` de `Program.cs`, modifique el registro del servicio <xref:System.Net.Http.HttpClient> existente para usar el cliente para leer el archivo:</span><span class="sxs-lookup"><span data-stu-id="44fc3-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="44fc3-128">Proveedor de configuración personalizada con EF Core</span><span class="sxs-lookup"><span data-stu-id="44fc3-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="44fc3-129">El proveedor de configuración personalizada con EF Core mostrado en <xref:fundamentals/configuration/index#custom-configuration-provider> funciona con aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="44fc3-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="44fc3-130">Las cadenas de conexión de base de datos y las bases de datos cargadas con aplicaciones Blazor WebAssembly no son seguras y no deben usarse para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="44fc3-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="44fc3-131">Agregue referencias de paquete para [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) y [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) al archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44fc3-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="44fc3-132">Agregue las clases de configuración de EF Core descritas en <xref:fundamentals/configuration/index#custom-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="44fc3-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="44fc3-133">Agregue espacios de nombres de <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> y <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="44fc3-134">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="44fc3-135">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="44fc3-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="44fc3-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-136">`Pages/EFCoreConfig.razor`:</span></span>

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="44fc3-137">Origen de la configuración de la memoria</span><span class="sxs-lookup"><span data-stu-id="44fc3-137">Memory Configuration Source</span></span>

<span data-ttu-id="44fc3-138">En el ejemplo siguiente se usa un elemento <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> en `Program.Main` para proporcionar configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="44fc3-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="44fc3-139">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="44fc3-140">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-140">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="44fc3-141">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="44fc3-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="44fc3-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-142">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="44fc3-143">Obtenga una sección de la configuración en el código de C# con <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="44fc3-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="44fc3-144">En el ejemplo siguiente se obtiene la sección `wheels` para la configuración del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="44fc3-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="44fc3-145">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="44fc3-145">Authentication configuration</span></span>

<span data-ttu-id="44fc3-146">Proporcione la configuración de autenticación en un archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44fc3-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="44fc3-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="44fc3-148">Cargue la configuración de un proveedor de Identity con <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="44fc3-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="44fc3-149">En el ejemplo siguiente se carga la configuración de un [proveedor de OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="44fc3-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="44fc3-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="44fc3-151">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="44fc3-151">Logging configuration</span></span>

<span data-ttu-id="44fc3-152">Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) al archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44fc3-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="44fc3-153">En el archivo de configuración de la aplicación, proporcione la configuración de registro.</span><span class="sxs-lookup"><span data-stu-id="44fc3-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="44fc3-154">La configuración de registro se carga en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="44fc3-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="44fc3-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="44fc3-156">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Logging?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="44fc3-157">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="44fc3-158">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="44fc3-158">Host builder configuration</span></span>

<span data-ttu-id="44fc3-159">Lea configuración del generador de host desde <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="44fc3-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="44fc3-160">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="44fc3-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="44fc3-161">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="44fc3-161">Cached configuration</span></span>

<span data-ttu-id="44fc3-162">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="44fc3-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="44fc3-163">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="44fc3-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="44fc3-164">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="44fc3-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="44fc3-165">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="44fc3-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="44fc3-166">Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="44fc3-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="44fc3-167">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="44fc3-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
