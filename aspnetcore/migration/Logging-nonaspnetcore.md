---
title: Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0
author: pakrym
description: Obtenga información sobre cómo migrar una aplicación de non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 a 2,2 o 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634103"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="a810a-103">Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0</span><span class="sxs-lookup"><span data-stu-id="a810a-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="a810a-104">En este artículo se describen los pasos comunes para migrar una aplicación de non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="a810a-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="a810a-105">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="a810a-105">2.1 to 2.2</span></span>

<span data-ttu-id="a810a-106">Crear `ServiceCollection` y llamar manualmente a `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="a810a-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="a810a-107">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="a810a-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a810a-108">ejemplo 2,2:</span><span class="sxs-lookup"><span data-stu-id="a810a-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="a810a-109">de 2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="a810a-109">2.1 to 3.0</span></span>

<span data-ttu-id="a810a-110">En 3,0, use `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="a810a-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="a810a-111">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="a810a-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a810a-112">ejemplo 3,0:</span><span class="sxs-lookup"><span data-stu-id="a810a-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="a810a-113">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a810a-113">Additional resources</span></span>

* <span data-ttu-id="a810a-114">[Paquete NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="a810a-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
