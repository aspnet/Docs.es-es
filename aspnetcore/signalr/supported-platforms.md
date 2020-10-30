---
title: Plataformas admitidas de SignalR de ASP.NET Core
author: bradygaster
description: Obtenga información sobre las plataformas admitidas en SignalR de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051023"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>Plataformas admitidas de SignalR de ASP.NET Core

## <a name="server-system-requirements"></a>Requisitos del sistema de servidor de

SignalR por ASP.NET Core admite cualquier plataforma de servidor que ASP.NET Core admita.

## <a name="javascript-client"></a>Cliente de JavaScript

El [cliente de JavaScript](xref:signalr/javascript-client) se ejecuta en NodeJS 8 y versiones posteriores, y en los siguientes exploradores:

| Explorador                          | Versión         |
| -------------------------------- | --------------- |
| Apple Safari, incluido iOS      | Current&dagger; |
| Google Chrome, incluido Android | Current&dagger; |
| Microsoft Edge                   | Current&dagger; |
| Mozilla Firefox                  | Current&dagger; |

&dagger;*Actual* se refiere a la versión más reciente del explorador.

## <a name="net-client"></a>Cliente .NET

El [cliente .net](xref:signalr/dotnet-client) se ejecuta en cualquier plataforma compatible con ASP.net Core. Por ejemplo, [los desarrolladores de Xamarin SignalR pueden usar](https://github.com/aspnet/Announcements/issues/305) para compilar aplicaciones de Android con Xamarin. Android 8.4.0.1 y las aplicaciones iOS y posteriores con Xamarin. iOS 11.14.0.4 y versiones posteriores.

Si el servidor ejecuta IIS, el transporte de WebSockets requiere IIS 8,0 o posterior en Windows Server 2012 o posterior. Se admiten otros transportes en todas las plataformas.

## <a name="java-client"></a>Cliente de Java

El [cliente de Java](xref:signalr/java-client) es compatible con Java 8 y versiones posteriores.

## <a name="unsupported-clients"></a>Clientes no compatibles

Los siguientes clientes están disponibles pero son experimentales o no oficiales. No se admiten actualmente y nunca pueden ser.

* [Cliente de C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Cliente SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
