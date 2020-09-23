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
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="ee225-103">Configuración del recortador de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ee225-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ee225-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="ee225-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="ee225-105">Blazor WebAssembly realiza un recorte de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) para reducir el tamaño de la salida publicada.</span><span class="sxs-lookup"><span data-stu-id="ee225-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="ee225-106">El recorte de una aplicación optimiza el tamaño, pero puede tener efectos perjudiciales.</span><span class="sxs-lookup"><span data-stu-id="ee225-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="ee225-107">Las aplicaciones que usan reflexión o características dinámicas relacionadas pueden verse interrumpidas cuando se recortan porque el recortador no conoce este comportamiento dinámico y, por lo general, no puede determinar los tipos que son necesarios para la reflexión en el tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ee225-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="ee225-108">Para recortar estas aplicaciones, se debe informar al recortador sobre los tipos necesarios para la reflexión en el código y en los paquetes o marcos de los que depende la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ee225-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="ee225-109">Para asegurarse de que la aplicación recortada funciona correctamente una vez implementada, es importante probar la salida publicada con frecuencia durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ee225-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="ee225-110">El recorte para aplicaciones .NET se puede deshabilitar estableciendo la propiedad `PublishTrimmed` de MSBuild en `false` en el archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ee225-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="ee225-111">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ee225-111">Additional resources</span></span>

* [<span data-ttu-id="ee225-112">Recorte de implementaciones autocontenidas y ejecutables</span><span class="sxs-lookup"><span data-stu-id="ee225-112">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
