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
# <a name="aspnet-core-blazor-configuration"></a>Configuración de Blazor en ASP.NET Core

> [!NOTE]
> Este tema se aplica a Blazor WebAssembly. Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.

Blazor WebAssembly carga la configuración desde:

* Archivos de configuración de aplicaciones de forma predeterminada:
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly. En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.

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

## <a name="provider-configuration"></a>Configuración de proveedor

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

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
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

Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

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
