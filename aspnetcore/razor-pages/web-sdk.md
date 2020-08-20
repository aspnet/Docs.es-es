---
title: SDK web de ASP.NET Core
author: Rick-Anderson
description: Información general sobre Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634818"
---
# <a name="aspnet-core-web-sdk"></a>SDK web de ASP.NET Core

### <a name="overview"></a>Información general

`Microsoft.NET.Sdk.Web` es un [SDK de proyecto de MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar aplicaciones de ASP.NET Core. Aunque se pueden compilar aplicaciones de ASP.NET Core sin el SDK web, dicho SDK:

* Está adaptado para proporcionar una experiencia al máximo nivel.
* Es el destino recomendado para la mayoría de los usuarios.

Use Web.SDK en un proyecto:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Características habilitadas al usar el SDK web:

* Los proyectos que tienen como destino .NET Core 3.0 o posterior hacen referencia implícitamente:

  * Al [marco de trabajo compartido de ASP.NET Core](xref:fundamentals/metapackage-app).
  * A [los analizadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) diseñados para compilar aplicaciones de ASP.NET Core.
* El SDK web importa destinos de MSBuild que permiten usar perfiles de publicación y realizar publicaciones mediante WebDeploy.

### <a name="properties"></a>Propiedades

| Propiedad. | Descripción |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Deshabilita la referencia implícita al marco de trabajo compartido `Microsoft.AspNetCore.App`. |
| `DisableImplicitAspNetCoreAnalyzers` | Deshabilita la referencia implícita a analizadores de ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Deshabilita la referencia implícita a analizadores de componentes de Razor cuando se compilan aplicaciones de Blazor (servidor). |
