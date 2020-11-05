---
title: Uso de ASP.NET Core con HTTP/2 en IIS
author: rick-anderson
description: Obtenga información sobre cómo usar las características de HTTP/2 con IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057419"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Uso de ASP.NET Core con HTTP/2 en IIS

Por [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación de IIS siguientes:

* Windows 2016 o posterior; Windows 10 o posterior
* IIS 10 o posterior
* Conexión con TLS 1.2 o una versión posterior
* Al [hospedar fuera del proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso al [servidor de Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.

Para una implementación dentro del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`. Para una implementación fuera del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/1.1`.

Para obtener más información sobre los modelos de hospedaje en proceso y fuera de proceso, consulte <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 está habilitado de forma predeterminada para las conexiones HTTPS/TLS. Las conexiones vuelven a HTTP/1.1 si no se establece una conexión HTTP/2. Para más información sobre la configuración HTTP/2 con implementaciones de IIS, vea [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Características avanzadas de HTTP/2 para admitir gRPC

Las características adicionales de HTTP/2 en IIS admiten gRPC, incluida la compatibilidad con los finalizadores de respuesta y el envío de marcos de restablecimiento.

Requisitos para ejecutar gRPC en IIS:

* Hospedaje dentro del proceso.
* Windows 10, compilación del sistema operativo 20300.1000 o posterior. Puede requerir el uso de compilaciones de Windows Insider.
* Conexión con TLS 1.2 o una versión posterior

### <a name="trailers"></a>Clips finales

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
