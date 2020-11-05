---
title: 'Registro de :::no-loc(Blazor)::: en ASP.NET Core'
author: guardrex
description: Obtenga información sobre cómo realizar registros en aplicaciones :::no-loc(Blazor):::, incluido cómo configurar el nivel de registro y cómo escribir mensajes de registro desde componentes :::no-loc(Razor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055989"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="6fabc-103">Registro de :::no-loc(Blazor)::: en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fabc-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="6fabc-104">Configure el registro en aplicaciones :::no-loc(Blazor WebAssembly)::: con la propiedad `WebAssemblyHostBuilder.Logging` establecida en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6fabc-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="6fabc-105">La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.</span><span class="sxs-lookup"><span data-stu-id="6fabc-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="6fabc-106">La configuración de registro se puede cargar desde archivos de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6fabc-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="6fabc-107">Para obtener más información, vea <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6fabc-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="6fabc-108">Para obtener instrucciones generales sobre el registro en ASP.NET Core, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="6fabc-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="6fabc-109">Registro de clientes de :::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET</span><span class="sxs-lookup"><span data-stu-id="6fabc-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="6fabc-110">Inserte un proveedor <xref:Microsoft.Extensions.Logging.ILoggerProvider> para agregar un registrador `WebAssemblyConsoleLogger` a los proveedores de registro pasados a <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6fabc-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="6fabc-111">A diferencia de los registradores <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> tradicionales, `WebAssemblyConsoleLogger` es un contenedor de API de registro específicas de explorador (por ejemplo, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="6fabc-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="6fabc-112">El uso de `WebAssemblyConsoleLogger` hace posible el registro en Mono dentro de un contexto de explorador.</span><span class="sxs-lookup"><span data-stu-id="6fabc-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="6fabc-113">Registro en componentes :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="6fabc-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="6fabc-114">Los registradores respetan la configuración de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6fabc-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="6fabc-115">La directiva `using` para <xref:Microsoft.Extensions.Logging> es necesaria con el fin de permitir las finalizaciones de IntelliSense para las API, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="6fabc-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="6fabc-116">En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILogger> en componentes :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="6fabc-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="6fabc-117">En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> en componentes :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="6fabc-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="6fabc-118">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6fabc-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
