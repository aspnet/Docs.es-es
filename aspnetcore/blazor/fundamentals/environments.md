---
title: Entornos de [Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre los entornos en [Blazor, incluido cómo configurar el entorno de una aplicación [Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/fundamentals/environments
ms.openlocfilehash: f8d0fc3cba22973628f405b4399cef39d562d6ed
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402902"
---
# <a name="aspnet-core-blazor-environments"></a>Entornos de [Blazor de ASP.NET Core

> [!NOTE]
> Este tema se aplica a [Blazor WebAssembly. Para obtener instrucciones generales sobre cómo configurar la aplicación ASP.NET Core, vea <xref:fundamentals/environments>.

Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo. Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.

Una aplicación [Blazor WebAssembly hospedada selecciona el entorno desde el servidor a través de un middleware que comunica el entorno con el explorador mediante la adición del encabezado `blazor-environment`. El valor del encabezado es el entorno. La aplicación [Blazor hospedada y la aplicación de servidor comparten el mismo entorno. Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.

En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo. Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.

En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo `web.config` publicado. El archivo `web.config` se encuentra en la carpeta `bin/Release/{TARGET FRAMEWORK}/publish`:

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

Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone el <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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
