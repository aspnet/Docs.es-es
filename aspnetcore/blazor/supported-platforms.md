---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280718"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Plataformas admitidas de Blazor de ASP.NET Core

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
