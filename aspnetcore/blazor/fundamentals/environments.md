---
title: Entornos de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre los entornos en Blazor, incluido cómo configurar el entorno de una aplicación Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 9ba23753df1726ee4c8a9802e1a1260ef7cf6fa5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506960"
---
# <a name="aspnet-core-no-locblazor-environments"></a>Entornos de Blazor de ASP.NET Core

> [!NOTE]
> Este tema se aplica a Blazor WebAssembly. Para obtener instrucciones generales sobre la configuración de la aplicación de ASP.NET Core, que describe los enfoques que se deben usar para las aplicaciones de Blazor Server, consulte <xref:fundamentals/environments>.

Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo. Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.

La aplicación de Blazor del lado cliente ( *`Client`* ) de una solución de Blazor WebAssembly hospedada determina el entorno a partir de la aplicación *`Server`* de la solución a través de un middleware que comunica el entorno con el explorador. La aplicación *`Server`* agrega un encabezado denominado `blazor-environment` con el entorno como valor del encabezado. La aplicación *`Client`* lee el encabezado. La aplicación *`Server`* de la solución es una aplicación ASP.NET Core, por lo que se encuentra más información sobre cómo configurar el entorno en <xref:fundamentals/environments>.

En el caso de una aplicación Blazor WebAssembly independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de desarrollo. Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.

En el ejemplo siguiente de IIS, se agrega el encabezado personalizado (`blazor-environment`) al archivo `web.config` publicado. El archivo `web.config` se encuentra en la carpeta `bin/Release/{TARGET FRAMEWORK}/publish`, donde el marcador de posición `{TARGET FRAMEWORK}` corresponde a la plataforma de destino:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Si quiere usar un archivo `web.config` personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta `publish`, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.

Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>.

`Pages/ReadEnvironment.razor`:

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que habilita una lógica específica del entorno en el código del generador de host.

En `Program.Main` de `Program.cs`:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Los métodos de extensión útiles siguientes proporcionados a través de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

En `Program.Main` de `Program.cs`:

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/environments>
