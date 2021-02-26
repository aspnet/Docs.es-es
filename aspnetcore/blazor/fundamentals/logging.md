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
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="4e3d0-103">Registro de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e3d0-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="4e3d0-104">Configure el registro personalizado en aplicaciones Blazor WebAssembly con la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="4e3d0-105">Agregue el espacio de nombres de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="4e3d0-106">En `Program.Main` de `Program.cs`, establezca el nivel de registro mínimo con <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> y agregue el proveedor de registro personalizado:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="4e3d0-107">La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="4e3d0-108">La configuración de registro se puede cargar desde archivos de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="4e3d0-109">Para obtener más información, vea <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="signalr-net-client-logging"></a><span data-ttu-id="4e3d0-110">Registro de clientes de SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="4e3d0-110">SignalR .NET client logging</span></span>

<span data-ttu-id="4e3d0-111">Inserte un proveedor <xref:Microsoft.Extensions.Logging.ILoggerProvider> para agregar un registrador `WebAssemblyConsoleLogger` a los proveedores de registro pasados a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="4e3d0-112">A diferencia de los registradores <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> tradicionales, `WebAssemblyConsoleLogger` es un contenedor de API de registro específicas de explorador (por ejemplo, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="4e3d0-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="4e3d0-113">El uso de `WebAssemblyConsoleLogger` hace posible el registro en Mono dentro de un contexto de explorador.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="4e3d0-114">`WebAssemblyConsoleLogger` es [interno](/dotnet/csharp/language-reference/keywords/internal) y no está disponible para el uso directo en el código para desarrolladores.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="4e3d0-115">Agregue el espacio de nombres de <xref:Microsoft.Extensions.Logging?displayProperty=fullName> e inserte un elemento <xref:Microsoft.Extensions.Logging.ILoggerProvider> en el componente:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="4e3d0-116">En el [método `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) del componente, use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="4e3d0-117">Para obtener instrucciones generales sobre el registro en ASP.NET Core que pertenece a Blazor Server, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-razor-components"></a><span data-ttu-id="4e3d0-118">Registro en componentes Razor</span><span class="sxs-lookup"><span data-stu-id="4e3d0-118">Log in Razor components</span></span>

<span data-ttu-id="4e3d0-119">Los registradores respetan la configuración de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="4e3d0-120">La directiva `using` para <xref:Microsoft.Extensions.Logging> es necesaria con el fin de permitir las finalizaciones de [IntelliSense](/visualstudio/ide/using-intellisense) para las API, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="4e3d0-121">En el siguiente ejemplo se muestra el registro con <xref:Microsoft.Extensions.Logging.ILogger> en componentes.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="4e3d0-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-122">`Pages/Counter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter1.razor?highlight=3,16)]

::: moniker-end

<span data-ttu-id="4e3d0-123">En el siguiente ejemplo se muestra el registro con <xref:Microsoft.Extensions.Logging.ILoggerFactory> en componentes.</span><span class="sxs-lookup"><span data-stu-id="4e3d0-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="4e3d0-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4e3d0-124">`Pages/Counter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/logging/Counter2.razor?highlight=3,16-17)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4e3d0-125">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4e3d0-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
