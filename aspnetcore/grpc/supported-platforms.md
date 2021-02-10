---
title: gRPC en plataformas compatibles con .NET
author: jamesnk
description: Conozca las plataformas admitidas para gRPC en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530169"
---
# <a name="grpc-on-net-supported-platforms"></a>gRPC en plataformas compatibles con .NET

Por [James Newton-King](https://twitter.com/jamesnk)

En este artículo se describen los requisitos y las plataformas admitidas para usar gRPC con .NET.

gRPC aprovecha las características avanzadas disponibles en HTTP/2. HTTP/2 no se admite en todas partes, pero hay disponible un segundo formato de conexión con HTTP/1.1 para gRPC:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 es la forma en que se suele usar gRPC.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica el protocolo gRPC para que sea compatible con HTTP/1.1. gRPC-Web se puede usar en más lugares, en particular, las aplicaciones del explorador pueden llamarla. Ya no se admiten dos características avanzadas de gRPC: streaming de cliente y streaming bidireccional.

gRPC en .NET admite ambos formatos de conexión. De forma predeterminada, se usa gRPC a través de HTTP/2. Para obtener información sobre la configuración de gRPC-Web, consulte <xref:grpc/browser>.

## <a name="device-requirements"></a>Requisitos de los dispositivos

gRPC en .NET admite cualquier dispositivo compatible con .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Exploradores&Dagger;

&dagger;[macOS no admite el hospedaje de aplicaciones ASP.NET Core con HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). Los clientes de gRPC en macOS pueden llamar a servicios remotos que usan HTTPS.

Las aplicaciones &Dagger;Blazor WebAssembly pueden llamar a servicios gRPC con gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Requisitos del servidor de ASP.NET Core

Los servicios gRPC se pueden hospedar en todos los servidores de ASP.NET Core integrados.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS requiere .NET 5 y Windows 10, compilación 20241 o posterior.

&Dagger;HTTP.sys requiere .NET 5 y Windows 10, compilación 19529 o posterior.

Para obtener información acerca de la configuración de servidores de ASP.NET Core para ejecutar gRPC, consulte <xref:grpc/aspnetcore#server-options>.

## <a name="net-version-requirements"></a>Requisitos de la versión de .NET

gRPC en .NET es compatible con .NET Core 3 y .NET 5 o posterior.

> [!div class="checklist"]
>
> * .NET 5 o posterior
> * .NET Core 3

gRPC en .NET no admite la ejecución en .NET Framework y Xamarin. La [biblioteca de gRPC C# core](https://grpc.io/docs/languages/csharp/quickstart/) es una biblioteca de terceros que admite .NET Framework y Xamarin. gRPC C# core no es compatible con Microsoft.

## <a name="azure-services"></a>Servicios de Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service no admite el hospedaje de gRPC a través de HTTP/2. gRPC-Web es una alternativa compatible.

Se está trabajando para mejorar la compatibilidad con gRPC con HTTP/2 en Azure App Service. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Recursos adicionales

* [Biblioteca de gRPC C# core](https://grpc.io/docs/languages/csharp/quickstart/)
