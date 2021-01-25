---
title: Cuándo usar un proxy inverso con el servidor web Kestrel de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cuándo usar un proxy inverso delante de Kestrel, el servidor web multiplataforma para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253887"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Cuándo usar Kestrel con un proxy inverso

Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/). Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.

Kestrel empleado como servidor web perimetral (accesible desde Internet):

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](_static/kestrel-to-internet2.png)

Kestrel empleado en una configuración de proxy inverso:

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](_static/kestrel-to-internet.png)

Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.

Cuando se usa Kestrel como un servidor perimetral sin un servidor proxy inverso, no se permite compartir la misma dirección IP y el mismo puerto entre varios procesos. Cuando se configura Kestrel para realizar escuchas en un puerto, controla todo el tráfico de ese puerto, con independencia de los encabezados `Host` de las solicitudes. Un proxy inverso que puede compartir puertos puede reenviar solicitudes a Kestrel en una única dirección IP y puerto.

Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.

Un proxy inverso puede hacer lo siguiente:

* Limitar el área expuesta públicamente de las aplicaciones que hospeda.
* Proporcionar una capa extra de configuración y defensa.
* Posiblemente, integrarse mejor con la infraestructura existente.
* Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS). Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.

> [!WARNING]
> El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Recursos adicionales

<xref:host-and-deploy/proxy-load-balancer>

