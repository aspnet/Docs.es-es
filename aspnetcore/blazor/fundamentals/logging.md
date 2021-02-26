---
title: Registro de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo realizar registros en aplicaciones Blazor, incluido cómo configurar el nivel de registro y cómo escribir mensajes de registro desde componentes Razor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e87b1a0dd54eb03dc1bdfdc7f2189a59df272026
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101033"
---
# <a name="aspnet-core-blazor-logging"></a>Registro de Blazor en ASP.NET Core

::: zone pivot="webassembly"

Configure el registro personalizado en aplicaciones Blazor WebAssembly con la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>.

Agregue el espacio de nombres de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

En `Program.Main` de `Program.cs`, establezca el nivel de registro mínimo con <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> y agregue el proveedor de registro personalizado:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.

La configuración de registro se puede cargar desde archivos de configuración de la aplicación. Para obtener más información, vea <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="signalr-net-client-logging"></a>Registro de clientes de SignalR .NET

Inserte un proveedor <xref:Microsoft.Extensions.Logging.ILoggerProvider> para agregar un registrador `WebAssemblyConsoleLogger` a los proveedores de registro pasados a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. A diferencia de los registradores <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> tradicionales, `WebAssemblyConsoleLogger` es un contenedor de API de registro específicas de explorador (por ejemplo, `console.log`). El uso de `WebAssemblyConsoleLogger` hace posible el registro en Mono dentro de un contexto de explorador.

> [!NOTE]
> `WebAssemblyConsoleLogger` es [interno](/dotnet/csharp/language-reference/keywords/internal) y no está disponible para el uso directo en el código para desarrolladores.

Agregue el espacio de nombres de <xref:Microsoft.Extensions.Logging?displayProperty=fullName> e inserte un elemento <xref:Microsoft.Extensions.Logging.ILoggerProvider> en el componente:

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

En el [método `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) del componente, use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Para obtener instrucciones generales sobre el registro en ASP.NET Core que pertenece a Blazor Server, consulte <xref:fundamentals/logging/index>.

::: zone-end

## <a name="log-in-razor-components"></a>Registro en componentes Razor

Los registradores respetan la configuración de inicio de la aplicación.

La directiva `using` para <xref:Microsoft.Extensions.Logging> es necesaria con el fin de permitir las finalizaciones de [IntelliSense](/visualstudio/ide/using-intellisense) para las API, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.

En el siguiente ejemplo se muestra el registro con <xref:Microsoft.Extensions.Logging.ILogger> en componentes.

`Pages/Counter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

En el siguiente ejemplo se muestra el registro con <xref:Microsoft.Extensions.Logging.ILoggerFactory> en componentes.

`Pages/Counter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
