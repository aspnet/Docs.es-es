---
title: Consideraciones de seguridad en gRPC para ASP.NET Core
author: jamesnk
description: Obtenga información sobre las consideraciones de seguridad de gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/security
ms.openlocfilehash: f06e239054b1c4edf126d1cf974dff1ca36ee56a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400133"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="2f465-103">Consideraciones de seguridad en gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f465-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="2f465-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2f465-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2f465-105">En este artículo se proporciona información sobre cómo proteger gRPC con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f465-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="2f465-106">Seguridad de transporte</span><span class="sxs-lookup"><span data-stu-id="2f465-106">Transport security</span></span>

<span data-ttu-id="2f465-107">Los mensajes gRPC se envían y se reciben mediante HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2f465-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="2f465-108">Es recomendable que:</span><span class="sxs-lookup"><span data-stu-id="2f465-108">We recommend:</span></span>

* <span data-ttu-id="2f465-109">Se use [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246) para proteger los mensajes en aplicaciones gRPC de producción.</span><span class="sxs-lookup"><span data-stu-id="2f465-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="2f465-110">Los servicios gRPC solo deben escuchar y responder a través de puertos seguros.</span><span class="sxs-lookup"><span data-stu-id="2f465-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="2f465-111">TLS esté configurado en Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2f465-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="2f465-112">Para obtener más información sobre cómo configurar puntos de conexión de Kestrel, vea [Configuración de puntos de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f465-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="2f465-113">Excepciones</span><span class="sxs-lookup"><span data-stu-id="2f465-113">Exceptions</span></span>

<span data-ttu-id="2f465-114">Los mensajes de excepción generalmente se consideran datos confidenciales que no se deben revelar a un cliente.</span><span class="sxs-lookup"><span data-stu-id="2f465-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="2f465-115">De forma predeterminada, gRPC no envía al cliente los detalles de una excepción iniciada por un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f465-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="2f465-116">En su lugar, el cliente recibe un mensaje genérico en el que se indica que se ha producido un error.</span><span class="sxs-lookup"><span data-stu-id="2f465-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="2f465-117">La entrega de mensajes de excepción al cliente se puede invalidar (por ejemplo, en desarrollo o prueba) con [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="2f465-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="2f465-118">Los mensajes de excepción no se deben exponer al cliente en las aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="2f465-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="2f465-119">Límites de tamaño de los mensajes</span><span class="sxs-lookup"><span data-stu-id="2f465-119">Message size limits</span></span>

<span data-ttu-id="2f465-120">Los mensajes entrantes a los clientes y servicios gRPC se cargan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="2f465-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="2f465-121">Los límites de tamaño de los mensajes son un mecanismo que ayuda a evitar que gRPC consuma demasiados recursos.</span><span class="sxs-lookup"><span data-stu-id="2f465-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="2f465-122">gRPC usa los límites de tamaño por mensaje para administrar los mensajes entrantes y salientes.</span><span class="sxs-lookup"><span data-stu-id="2f465-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="2f465-123">De forma predeterminada, gRPC limita los mensajes entrantes a 4 MB.</span><span class="sxs-lookup"><span data-stu-id="2f465-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="2f465-124">No hay ningún límite para los mensajes salientes.</span><span class="sxs-lookup"><span data-stu-id="2f465-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="2f465-125">En el servidor, los límites de los mensajes de gRPC se pueden configurar para todos los servicios de una aplicación con `AddGrpc`:</span><span class="sxs-lookup"><span data-stu-id="2f465-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="2f465-126">Los límites también se pueden configurar para un servicio individual mediante `AddServiceOptions<TService>`.</span><span class="sxs-lookup"><span data-stu-id="2f465-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="2f465-127">Para obtener más información sobre cómo configurar los límites de tamaño de los mensajes, vea [Configuración de gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="2f465-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="2f465-128">Validación de certificados de cliente</span><span class="sxs-lookup"><span data-stu-id="2f465-128">Client certificate validation</span></span>

<span data-ttu-id="2f465-129">Los [certificados de cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) se validan inicialmente cuando se establece la conexión.</span><span class="sxs-lookup"><span data-stu-id="2f465-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="2f465-130">De forma predeterminada, Kestrel no realiza ninguna validación adicional del certificado de cliente de una conexión.</span><span class="sxs-lookup"><span data-stu-id="2f465-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="2f465-131">Se recomienda que los servicios gRPC protegidos por certificados de cliente usen el paquete [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth).</span><span class="sxs-lookup"><span data-stu-id="2f465-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="2f465-132">La autenticación de certificación de ASP.NET Core realizará una validación adicional en un certificado de cliente, lo que incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2f465-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="2f465-133">El certificado tiene un uso mejorado de clave (EKU) válido.</span><span class="sxs-lookup"><span data-stu-id="2f465-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="2f465-134">Está dentro de su período de validez.</span><span class="sxs-lookup"><span data-stu-id="2f465-134">Is within its validity period</span></span>
* <span data-ttu-id="2f465-135">Se comprueba la revocación de certificados.</span><span class="sxs-lookup"><span data-stu-id="2f465-135">Check certificate revocation</span></span>
