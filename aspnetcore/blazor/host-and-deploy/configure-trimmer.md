---
title: Configuración del recortador de ASP.NET Core Blazor
author: guardrex
description: Aprenda a controlar el enlazador de lenguaje intermedio (IL) (recortador) al compilar una aplicación Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055807"
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
Encontrará más opciones para configurar el recortador en [Opciones de recorte](/dotnet/core/deploying/trimming-options).

## <a name="additional-resources"></a>Recursos adicionales

* [Recorte de implementaciones autocontenidas y ejecutables](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
