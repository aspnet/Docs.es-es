---
title: Uso de HTTP/2 con el servidor web Kestrel de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar HTTP/2 con Kestrel, el servidor web multiplataforma de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253893"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Uso de HTTP/2 con el servidor web Kestrel de ASP.NET Core

[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:

* Sistema operativo&dagger;
  * Windows Server 2016/Windows 10 o posterior&Dagger;
  * Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)
* Plataforma de destino: .NET Core 2.2 o posterior
* Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Conexión con TLS 1.2 o una versión posterior

&dagger;HTTP/2 se admitirá en una versión futura en macOS.
&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1. La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada. Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.

Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) notifica `HTTP/2`.

A partir de .NET Core 3.0, HTTP/2 está habilitado de forma predeterminada. Para obtener más información sobre la configuración, vea las secciones [Límites HTTP/2 de Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) y [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).

## <a name="advanced-http2-features"></a>Características avanzadas de HTTP/2

Las características adicionales de HTTP/2 en Kestrel admiten gRPC, incluida la compatibilidad con los finalizadores de respuesta y el envío de marcos de restablecimiento.

### <a name="trailers"></a>Clips finales

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
