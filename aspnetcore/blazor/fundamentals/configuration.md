---
title: Configuración de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar aplicaciones Blazor, incluidas opciones de configuración de la aplicación, la autenticación y el registro.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 3e1d265f269e2a2c5ea584e62e80f8a6167f9d3a
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944411"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="4279f-103">Configuración de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4279f-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="4279f-104">Este tema se aplica a Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="4279f-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4279f-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4279f-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor WebAssembly<span data-ttu-id="4279f-106"> carga la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="4279f-106"> loads configuration from:</span></span>

* <span data-ttu-id="4279f-107">Archivos de configuración de aplicaciones de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="4279f-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="4279f-108">Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación</span><span class="sxs-lookup"><span data-stu-id="4279f-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="4279f-109">No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="4279f-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="4279f-110">En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="4279f-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="4279f-111">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="4279f-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4279f-112">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="4279f-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="4279f-113">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4279f-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="4279f-114">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="4279f-114">App settings configuration</span></span>

<span data-ttu-id="4279f-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4279f-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="4279f-116">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="4279f-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="4279f-117">Configuración de proveedor</span><span class="sxs-lookup"><span data-stu-id="4279f-117">Provider configuration</span></span>

<span data-ttu-id="4279f-118">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="4279f-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="4279f-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4279f-119">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

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

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="4279f-120">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="4279f-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="4279f-121">Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="4279f-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="4279f-122">Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4279f-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="4279f-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="4279f-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="4279f-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4279f-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="4279f-125">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="4279f-125">Authentication configuration</span></span>

<span data-ttu-id="4279f-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4279f-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="4279f-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4279f-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="4279f-128">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="4279f-128">Logging configuration</span></span>

<span data-ttu-id="4279f-129">Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="4279f-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="4279f-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4279f-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="4279f-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4279f-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="4279f-132">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="4279f-132">Host builder configuration</span></span>

<span data-ttu-id="4279f-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4279f-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="4279f-134">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="4279f-134">Cached configuration</span></span>

<span data-ttu-id="4279f-135">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="4279f-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4279f-136">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="4279f-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4279f-137">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4279f-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4279f-138">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="4279f-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4279f-139">Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="4279f-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4279f-140">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="4279f-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
