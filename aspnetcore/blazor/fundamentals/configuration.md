---
title: Configuración de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar aplicaciones Blazor, incluidas opciones de configuración de la aplicación, la autenticación y el registro.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234379"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configuración de Blazor en ASP.NET Core

> [!NOTE]
> Este tema se aplica a Blazor WebAssembly. Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.

Blazor WebAssembly carga la configuración de los archivos de configuración de la aplicación de forma predeterminada:

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

Otros proveedores de configuración registrados por la aplicación también pueden proporcionar la configuración.

No todos los proveedores ni todas las características de los proveedores son adecuados para las aplicaciones Blazor WebAssembly:

* [Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration): no se admite el proveedor para identidad administrada e identificador de aplicación (Id. de cliente) con escenarios de secreto de cliente. No se recomienda el Id. de aplicación con un secreto de cliente para ninguna aplicación ASP.NET Core, especialmente las aplicaciones Blazor WebAssembly, porque el secreto de cliente no se puede proteger en el lado cliente para acceder al servicio.
* [Proveedor de configuración de aplicaciones de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): el proveedor no es adecuado para las aplicaciones Blazor WebAssembly porque las aplicaciones Blazor WebAssembly no se ejecutan en un servidor de Azure.

> [!WARNING]
> La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios. **No almacene credenciales ni secretos de aplicación en la configuración.**

Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Configuración de aplicaciones

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>Proveedor de configuración personalizada con EF Core

El proveedor de configuración personalizada con EF Core mostrado en <xref:fundamentals/configuration/index#custom-configuration-provider> funciona con aplicaciones Blazor WebAssembly.

Agregue el proveedor de configuración del ejemplo con el código siguiente en `Program.Main` (`Program.cs`):

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>Origen de la configuración de la memoria

En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:

`Program.Main`:

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

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

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

Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo. Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

## <a name="authentication-configuration"></a>Configuración de autenticación

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuración del registro

Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).

`wwwroot/appsettings.json`:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuración del generador de host

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuración almacenada en caché

Los archivos de configuración se almacenan en caché para usarlos sin conexión. Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva. Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:

* Los usuarios tienen versiones en caché de los archivos que siguen usando.
* Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.

Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.
