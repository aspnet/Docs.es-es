---
title: Configuración del recortador de ASP.NET Core Blazor
author: guardrex
description: Aprenda a controlar el enlazador de lenguaje intermedio (IL) (recortador) al compilar una aplicación Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080853"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Configuración del recortador de ASP.NET Core Blazor

Por [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly realiza un recorte de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) para reducir el tamaño de la salida publicada.

El recorte de una aplicación optimiza el tamaño, pero puede tener efectos perjudiciales. Las aplicaciones que usan reflexión o características dinámicas relacionadas pueden verse interrumpidas cuando se recortan porque el recortador no conoce este comportamiento dinámico y, por lo general, no puede determinar los tipos que son necesarios para la reflexión en el tiempo de ejecución. Para recortar estas aplicaciones, se debe informar al recortador sobre los tipos necesarios para la reflexión en el código y en los paquetes o marcos de los que depende la aplicación.

Para asegurarse de que la aplicación recortada funciona correctamente una vez implementada, es importante probar la salida publicada con frecuencia durante el desarrollo.

El recorte para aplicaciones .NET se puede deshabilitar estableciendo la propiedad `PublishTrimmed` de MSBuild en `false` en el archivo de proyecto de la aplicación:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a>Recursos adicionales

* [Recorte de implementaciones autocontenidas y ejecutables](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
