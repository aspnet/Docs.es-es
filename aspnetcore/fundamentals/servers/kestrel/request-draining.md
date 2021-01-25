---
title: Vaciado de solicitudes con el servidor web Kestrel de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el vaciado de solicitudes con Kestrel, el servidor web multiplataforma de ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253891"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="b2aa8-103">Vaciado de solicitudes con el servidor web Kestrel de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2aa8-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="b2aa8-104">La apertura de conexiones HTTP lleva mucho tiempo.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="b2aa8-105">En HTTPS, además consume muchos recursos.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="b2aa8-106">Por lo tanto, Kestrel intenta reutilizar las conexiones conforme al protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="b2aa8-107">El cuerpo de una solicitud debe usarse por completo para que se vuelva a usar la conexión.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="b2aa8-108">La aplicación no siempre consume el cuerpo de la solicitud, como las solicitudes HTTP POST en las que el servidor devuelve un redireccionamiento o una respuesta 404.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="b2aa8-109">En el caso de redireccionamiento de HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="b2aa8-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="b2aa8-110">Es posible que el cliente ya haya enviado parte de los datos de POST.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="b2aa8-111">El servidor escribe la respuesta 301.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="b2aa8-112">La conexión no se puede usar para una nueva solicitud hasta que los datos POST del cuerpo de la solicitud anterior se hayan leído en su totalidad.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="b2aa8-113">Kestrel intenta vaciar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="b2aa8-114">El vaciado del cuerpo de la solicitud significa leer y descartar los datos sin procesarlos.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="b2aa8-115">El proceso de vaciado llega a un equilibrio entre permitir que se vuelva a usar la conexión y el tiempo que se tarda en vaciar los datos restantes:</span><span class="sxs-lookup"><span data-stu-id="b2aa8-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="b2aa8-116">El vaciado tiene un tiempo de espera de cinco segundos que no se puede configurar.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="b2aa8-117">Si no se han leído todos los datos especificados por el encabezado `Content-Length` o `Transfer-Encoding` antes de que se agote el tiempo de espera, se cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="b2aa8-118">A veces, puede que quiera finalizar la solicitud inmediatamente, antes o después de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="b2aa8-119">Por ejemplo, los clientes pueden tener límites de datos restrictivos.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="b2aa8-120">La limitación de los datos cargados puede ser una prioridad.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="b2aa8-121">En tales casos, para finalizar una solicitud, llame a [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) desde un controlador, una página de Razor o un middleware.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="b2aa8-122">Hay advertencias respecto a la llamada a `Abort`:</span><span class="sxs-lookup"><span data-stu-id="b2aa8-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="b2aa8-123">La creación de nuevas conexiones puede ser lenta y costosa.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="b2aa8-124">No hay ninguna garantía de que el cliente haya leído la respuesta antes de que se cierre la conexión.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="b2aa8-125">La llamada a `Abort` debe ser poco frecuente y reservada para casos de error graves, no comunes.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="b2aa8-126">Llame a `Abort` solo cuando sea necesario resolver un problema concreto.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="b2aa8-127">Por ejemplo, llame a `Abort` si hay clientes malintencionados que intentan aplicar POST a los datos, o bien si hay un error en el código cliente que genera varias solicitudes o de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="b2aa8-128">No llame a `Abort` para situaciones de error comunes, como HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="b2aa8-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="b2aa8-129">La llamada a [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes de llamar a `Abort` garantiza que el servidor haya terminado de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="b2aa8-130">Pero el comportamiento del cliente no es predecible y es posible que no lea la respuesta antes de que se anule la conexión.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="b2aa8-131">Este proceso es diferente para HTTP/2, ya que el protocolo admite la anulación de flujos de solicitud individuales sin cerrar la conexión.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="b2aa8-132">El tiempo de espera de vaciado de cinco segundos no se aplica.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="b2aa8-133">Si hay datos del cuerpo de la solicitud sin leer después de completar una respuesta, el servidor envía un marco RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="b2aa8-134">Se omiten los marcos de datos del cuerpo de la solicitud adicionales.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="b2aa8-135">Si es posible, es mejor que los clientes usen el encabezado de solicitud [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) y esperen a que el servidor responda para empezar a enviar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="b2aa8-136">Esto proporciona al cliente la oportunidad de examinar la respuesta y anular antes de enviar datos innecesarios.</span><span class="sxs-lookup"><span data-stu-id="b2aa8-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
