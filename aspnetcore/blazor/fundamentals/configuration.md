---
title: 'Configuración de :::no-loc(Blazor)::: en ASP.NET Core'
author: guardrex
description: Obtenga información sobre cómo configurar aplicaciones :::no-loc(Blazor):::, incluidas opciones de configuración de la aplicación, la autenticación y el registro.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234379"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="71809-103">Configuración de :::no-loc(Blazor)::: en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71809-103">ASP.NET Core :::no-loc(Blazor)::: configuration</span></span>

> [!NOTE]
> <span data-ttu-id="71809-104">Este tema se aplica a :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="71809-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="71809-105">Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71809-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="71809-106">:::no-loc(Blazor WebAssembly)::: carga la configuración de los archivos de configuración de la aplicación de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="71809-106">:::no-loc(Blazor WebAssembly)::: loads configuration from app settings files by default:</span></span>

* `wwwroot/:::no-loc(appsettings.json):::`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="71809-107">Otros proveedores de configuración registrados por la aplicación también pueden proporcionar la configuración.</span><span class="sxs-lookup"><span data-stu-id="71809-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="71809-108">No todos los proveedores ni todas las características de los proveedores son adecuados para las aplicaciones :::no-loc(Blazor WebAssembly)::::</span><span class="sxs-lookup"><span data-stu-id="71809-108">Not all providers or provider features are appropriate for :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="71809-109">[Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration): no se admite el proveedor para identidad administrada e identificador de aplicación (Id. de cliente) con escenarios de secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="71809-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="71809-110">No se recomienda el Id. de aplicación con un secreto de cliente para ninguna aplicación ASP.NET Core, especialmente las aplicaciones :::no-loc(Blazor WebAssembly):::, porque el secreto de cliente no se puede proteger en el lado cliente para acceder al servicio.</span><span class="sxs-lookup"><span data-stu-id="71809-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially :::no-loc(Blazor WebAssembly)::: apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="71809-111">[Proveedor de configuración de aplicaciones de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): el proveedor no es adecuado para las aplicaciones :::no-loc(Blazor WebAssembly)::: porque las aplicaciones :::no-loc(Blazor WebAssembly)::: no se ejecutan en un servidor de Azure.</span><span class="sxs-lookup"><span data-stu-id="71809-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for :::no-loc(Blazor WebAssembly)::: apps because :::no-loc(Blazor WebAssembly)::: apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="71809-112">La configuración de una aplicación :::no-loc(Blazor WebAssembly)::: es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="71809-112">Configuration in a :::no-loc(Blazor WebAssembly)::: app is visible to users.</span></span> <span data-ttu-id="71809-113">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="71809-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="71809-114">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71809-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="71809-115">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="71809-115">App settings configuration</span></span>

<span data-ttu-id="71809-116">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="71809-116">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="71809-117">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="71809-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="71809-118">Proveedor de configuración personalizada con EF Core</span><span class="sxs-lookup"><span data-stu-id="71809-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="71809-119">El proveedor de configuración personalizada con EF Core mostrado en <xref:fundamentals/configuration/index#custom-configuration-provider> funciona con aplicaciones :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="71809-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="71809-120">Agregue el proveedor de configuración del ejemplo con el código siguiente en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="71809-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="71809-121">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="71809-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="71809-122">Origen de la configuración de la memoria</span><span class="sxs-lookup"><span data-stu-id="71809-122">Memory Configuration Source</span></span>

<span data-ttu-id="71809-123">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="71809-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="71809-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71809-124">`Program.Main`:</span></span>

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

<span data-ttu-id="71809-125">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="71809-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="71809-126">Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="71809-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="71809-127">Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="71809-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="71809-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="71809-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="71809-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71809-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="71809-130">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="71809-130">Authentication configuration</span></span>

<span data-ttu-id="71809-131">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="71809-131">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="71809-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71809-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="71809-133">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="71809-133">Logging configuration</span></span>

<span data-ttu-id="71809-134">Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span><span class="sxs-lookup"><span data-stu-id="71809-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="71809-135">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="71809-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="71809-136">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="71809-136">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

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

<span data-ttu-id="71809-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71809-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="71809-138">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="71809-138">Host builder configuration</span></span>

<span data-ttu-id="71809-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71809-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="71809-140">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="71809-140">Cached configuration</span></span>

<span data-ttu-id="71809-141">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="71809-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="71809-142">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="71809-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="71809-143">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="71809-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="71809-144">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="71809-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="71809-145">Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="71809-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="71809-146">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="71809-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
