---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754546"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>Plataformas admitidas de Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly y Blazor Server se admiten en los exploradores que se muestran en la tabla siguiente.

| Explorador                          | Versión         |
| -------------------------------- | --------------- |
| Apple Safari, incluido iOS      | Current&dagger; |
| Google Chrome, incluido Android | Current&dagger; |
| Microsoft Edge                   | Current&dagger; |
| Mozilla Firefox                  | Current&dagger; |  

&dagger;*Actual* se refiere a la versión más reciente del explorador.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Explorador                          | Versión               |
| -------------------------------- | --------------------- |
| Apple Safari, incluido iOS      | Current&dagger;       |
| Google Chrome, incluido Android | Current&dagger;       |
| Microsoft Edge                   | Current&dagger;       |
| Microsoft Internet Explorer      | No admitidas&Dagger; |
| Mozilla Firefox                  | Current&dagger;       |  

&dagger;*Actual* se refiere a la versión más reciente del explorador.  
&Dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).

## Blazor Server

| Explorador                          | Versión         |
| -------------------------------- | --------------- |
| Apple Safari, incluido iOS      | Current&dagger; |
| Google Chrome, incluido Android | Current&dagger; |
| Microsoft Edge                   | Current&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Current&dagger; |

&dagger;*Actual* se refiere a la versión más reciente del explorador.  
&Dagger;Se requieren elementos polyfill adicionales. Por ejemplo, las promesas se pueden agregar mediante un conjunto de [`Polyfill.io`](https://polyfill.io/v3/).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
