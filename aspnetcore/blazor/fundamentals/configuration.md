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
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485972"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configuración de Blazor en ASP.NET Core

> [!NOTE]
> Este tema se aplica a Blazor WebAssembly. Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/configuration/index>.

Blazor WebAssembly carga la configuración de los siguientes archivos de configuración de la aplicación de forma predeterminada:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, donde el marcador de posición `{ENVIRONMENT}` es el [entorno en tiempo de ejecución](xref:fundamentals/environments) de la aplicación.

Otros proveedores de configuración registrados por la aplicación también pueden proporcionar la configuración, pero no todos los proveedores o características de proveedor son adecuados para aplicaciones Blazor WebAssembly:

* [Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration): no se admite el proveedor para identidad administrada e identificador de aplicación (Id. de cliente) con escenarios de secreto de cliente. No se recomienda usar el identificador de la aplicación con un secreto de cliente para ninguna aplicación ASP.NET Core, especialmente las aplicaciones Blazor WebAssembly, porque el secreto de cliente no se puede proteger en el lado cliente para acceder al servicio Azure Key Vault.
* [Proveedor de configuración de aplicaciones de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): el proveedor no es adecuado para las aplicaciones Blazor WebAssembly porque las aplicaciones Blazor WebAssembly no se ejecutan en un servidor de Azure.

> [!WARNING]
> La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios. **No almacene secretos de aplicación, credenciales ni otros datos confidenciales en la configuración de una aplicación Blazor WebAssembly.**

Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Configuración de aplicaciones

La configuración de los archivos de configuración de la aplicación se carga de forma predeterminada. En el siguiente ejemplo, un valor de configuración de la interfaz de usuario se almacena en un archivo de configuración de la aplicación y lo carga el marco Blazor automáticamente. Un componente lee el valor.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Para leer otros archivos de configuración de la carpeta `wwwroot` en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo. En el ejemplo siguiente se lee un archivo de configuración (`cars.json`) en la configuración de la aplicación.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.Extensions.Configuration;
```

En `Program.Main` de `Program.cs`, modifique el registro del servicio <xref:System.Net.Http.HttpClient> existente para usar el cliente para leer el archivo:

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

## <a name="custom-configuration-provider-with-ef-core"></a>Proveedor de configuración personalizada con EF Core

El proveedor de configuración personalizada con EF Core mostrado en <xref:fundamentals/configuration/index#custom-configuration-provider> funciona con aplicaciones Blazor WebAssembly.

> [!WARNING]
> Las cadenas de conexión de base de datos y las bases de datos cargadas con aplicaciones Blazor WebAssembly no son seguras y no deben usarse para almacenar información confidencial.

Agregue referencias de paquete para [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) y [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) al archivo de proyecto de la aplicación.

Agregue las clases de configuración de EF Core descritas en <xref:fundamentals/configuration/index#custom-configuration-provider>.

Agregue espacios de nombres de <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> y <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

En `Program.Main` de `Program.cs`:

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.

`Pages/EFCoreConfig.razor`:

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

## <a name="memory-configuration-source"></a>Origen de la configuración de la memoria

En el ejemplo siguiente se usa un elemento <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> en `Program.Main` para proporcionar configuración adicional.

Agregue el espacio de nombres de <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

En `Program.Main` de `Program.cs`:

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

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración.

`Pages/MemoryConfig.razor`:

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

Obtenga una sección de la configuración en el código de C# con <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>. En el ejemplo siguiente se obtiene la sección `wheels` para la configuración del ejemplo anterior:

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Configuración de autenticación

Proporcione la configuración de autenticación en un archivo de configuración de la aplicación.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Cargue la configuración de un proveedor de Identity con <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> en `Program.Main`. En el ejemplo siguiente se carga la configuración de un [proveedor de OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuración del registro

Agregue una referencia de paquete para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) al archivo de proyecto de la aplicación. En el archivo de configuración de la aplicación, proporcione la configuración de registro. La configuración de registro se carga en `Program.Main`.

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

Agregue el espacio de nombres de <xref:Microsoft.Extensions.Logging?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.Extensions.Logging;
```

En `Program.Main` de `Program.cs`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuración del generador de host

Lea configuración del generador de host desde <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> en `Program.Main`.

En `Program.Main` de `Program.cs`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuración almacenada en caché

Los archivos de configuración se almacenan en caché para usarlos sin conexión. Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva. Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:

* Los usuarios tienen versiones en caché de los archivos que siguen usando.
* Los archivos `service-worker.js` y `service-worker-assets.js` de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.

Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.
