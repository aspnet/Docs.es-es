---
title: Elección entre ASP.NET 4.x y ASP.NET Core
author: rick-anderson
description: Se explican las diferencias entre ASP.NET Core. y ASP.NET 4.x, y cómo elegir entre ellos.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 29596f1d974d585570af5ad686ff6be4b1f9b837
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017498"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Elección entre ASP.NET 4.x y ASP.NET Core

ASP.NET Core es un rediseño de ASP.NET 4.x. En este artículo se enumeran las diferencias entre ellos.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core es un marco multiplataforma de código abierto que tiene como finalidad compilar modernas aplicaciones web basadas en la nube en Windows, macOS o Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x es un marco consolidado que proporciona los servicios necesarios para compilar aplicaciones web de nivel empresarial basadas en servidor en Windows.

## <a name="framework-selection"></a>Selección del marco

En la tabla siguiente se compara ASP.NET Core en ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Compilación para Windows, macOS o Linux|Compilación para Windows|
|[Razor Pages](xref:razor-pages/index) es el método recomendado para crear una interfaz de usuario web a partir de ASP.NET Core 2.x. Consulte también [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api) y [SignalR](xref:signalr/introduction).|Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/) o [Web Pages](/aspnet/web-pages)|
|Varias versiones por equipo|Una versión por equipo|
|Desarrollo con [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) o [Visual Studio Code](https://code.visualstudio.com/) con C# o F#|Desarrollo con [Visual Studio](https://visualstudio.microsoft.com/vs/) con C#, VB o F#|
|Mayor rendimiento que ASP.NET 4.x|Buen rendimiento|
|[Uso del entorno de ejecución de .NET Core](/dotnet/standard/choosing-core-framework-server)|Usar el tiempo de ejecución de .NET Framework|

Vea [ASP.NET Core con .NET Framework como destino](xref:index#target-framework) para obtener información sobre la compatibilidad de ASP.NET Core 2.x en .NET Framework.

## <a name="aspnet-core-scenarios"></a>Escenarios de ASP.NET Core

* [Sitios web](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)
* [En tiempo real](xref:signalr/introduction)
* [Implementación de una aplicación ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>Escenarios de ASP.NET 4.x

* [Sitios web](/aspnet/mvc)
* [API](/aspnet/web-api)
* [En tiempo real](/aspnet/signalr)
* [Creación de una aplicación web ASP.NET 4.x en Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción a ASP.NET](/aspnet/overview)
* [Introducción a ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
