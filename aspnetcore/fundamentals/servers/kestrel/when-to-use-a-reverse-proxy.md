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
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6775f-103">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="6775f-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6775f-104">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6775f-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6775f-105">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6775f-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6775f-106">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="6775f-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](_static/kestrel-to-internet2.png)

<span data-ttu-id="6775f-108">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="6775f-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](_static/kestrel-to-internet.png)

<span data-ttu-id="6775f-110">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="6775f-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6775f-111">Cuando se usa Kestrel como un servidor perimetral sin un servidor proxy inverso, no se permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="6775f-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="6775f-112">Cuando se configura Kestrel para realizar escuchas en un puerto, controla todo el tráfico de ese puerto, con independencia de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6775f-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6775f-113">Un proxy inverso que puede compartir puertos puede reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6775f-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6775f-114">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="6775f-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6775f-115">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6775f-115">A reverse proxy:</span></span>

* <span data-ttu-id="6775f-116">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="6775f-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6775f-117">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="6775f-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6775f-118">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="6775f-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6775f-119">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6775f-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6775f-120">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="6775f-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6775f-121">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6775f-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6775f-122">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6775f-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

