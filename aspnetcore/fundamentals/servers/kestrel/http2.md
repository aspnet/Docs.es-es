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
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="75fc7-103">Uso de HTTP/2 con el servidor web Kestrel de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75fc7-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="75fc7-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="75fc7-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="75fc7-105">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="75fc7-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="75fc7-106">Windows Server 2016/Windows 10 o posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="75fc7-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="75fc7-107">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="75fc7-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="75fc7-108">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="75fc7-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="75fc7-109">Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="75fc7-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="75fc7-110">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="75fc7-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="75fc7-111">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="75fc7-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="75fc7-112">&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="75fc7-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="75fc7-113">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="75fc7-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="75fc7-114">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="75fc7-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="75fc7-115">Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="75fc7-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="75fc7-116">A partir de .NET Core 3.0, HTTP/2 está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="75fc7-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="75fc7-117">Para obtener más información sobre la configuración, vea las secciones [Límites HTTP/2 de Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) y [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="75fc7-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="75fc7-118">Características avanzadas de HTTP/2</span><span class="sxs-lookup"><span data-stu-id="75fc7-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="75fc7-119">Las características adicionales de HTTP/2 en Kestrel admiten gRPC, incluida la compatibilidad con los finalizadores de respuesta y el envío de marcos de restablecimiento.</span><span class="sxs-lookup"><span data-stu-id="75fc7-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="75fc7-120">Clips finales</span><span class="sxs-lookup"><span data-stu-id="75fc7-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="75fc7-121">Reset</span><span class="sxs-lookup"><span data-stu-id="75fc7-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
