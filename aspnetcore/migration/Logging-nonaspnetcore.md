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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0

En este artículo se describen los pasos comunes para migrar una aplicación de non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 a 2,2 o 3,0.

## <a name="21-to-22"></a>2.1 a 2.2

Crear `ServiceCollection` y llamar manualmente a `AddLogging` .

ejemplo 2,1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

ejemplo 2,2:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>de 2,1 a 3,0

En 3,0, use `LoggingFactory.Create` .

ejemplo 2,1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

ejemplo 3,0:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Recursos adicionales

* [Paquete NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
