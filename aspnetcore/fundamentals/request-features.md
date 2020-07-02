---
title: Características de solicitud de ASP.NET Core
author: ardalis
description: Obtenga información sobre los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas que se definen en las interfaces de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: 21e78c6c1d05eb3b2ce8ade1b950b15823fa0e83
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407283"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="0a47b-103">Características de solicitud de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a47b-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="0a47b-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0a47b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0a47b-105">Los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas se definen en las interfaces.</span><span class="sxs-lookup"><span data-stu-id="0a47b-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="0a47b-106">Estas interfaces se usan en implementaciones de servidor web y middleware para crear y modificar la canalización de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a47b-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="0a47b-107">Interfaces de características</span><span class="sxs-lookup"><span data-stu-id="0a47b-107">Feature interfaces</span></span>

<span data-ttu-id="0a47b-108">ASP.NET Core define una serie de interfaces de características HTTP en `Microsoft.AspNetCore.Http.Features` que los servidores usan para identificar las características que admiten.</span><span class="sxs-lookup"><span data-stu-id="0a47b-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="0a47b-109">Las siguientes interfaces de características controlan las solicitudes y devuelven respuestas:</span><span class="sxs-lookup"><span data-stu-id="0a47b-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="0a47b-110">`IHttpRequestFeature` Define la estructura de una solicitud HTTP; esto es, el protocolo, la ruta de acceso, la cadena de consulta, los encabezados y el cuerpo.</span><span class="sxs-lookup"><span data-stu-id="0a47b-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="0a47b-111">`IHttpResponseFeature` Define la estructura de una respuesta HTTP; esto es, el código de estado, los encabezados y el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a47b-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="0a47b-112">`IHttpAuthenticationFeature` Define la capacidad para identificar usuarios según un `ClaimsPrincipal` y para especificar un controlador de autenticación.</span><span class="sxs-lookup"><span data-stu-id="0a47b-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="0a47b-113">`IHttpUpgradeFeature` Define la compatibilidad con [actualizaciones HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permiten al cliente especificar otros protocolos que le gustaría usar en caso de que el servidor cambie de protocolo.</span><span class="sxs-lookup"><span data-stu-id="0a47b-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="0a47b-114">`IHttpBufferingFeature` Define métodos para deshabilitar el almacenamiento en búfer de solicitudes o respuestas.</span><span class="sxs-lookup"><span data-stu-id="0a47b-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="0a47b-115">`IHttpConnectionFeature` Define las propiedades de las direcciones y los puertos tanto locales como remotos.</span><span class="sxs-lookup"><span data-stu-id="0a47b-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="0a47b-116">`IHttpRequestLifetimeFeature` Define la capacidad para anular conexiones o para detectar si una solicitud ha finalizado antes de tiempo (por ejemplo, debido a una desconexión del cliente).</span><span class="sxs-lookup"><span data-stu-id="0a47b-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="0a47b-117">`IHttpSendFileFeature` Define un método para enviar archivos de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="0a47b-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="0a47b-118">`IHttpWebSocketFeature` Define una API para admitir sockets web.</span><span class="sxs-lookup"><span data-stu-id="0a47b-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="0a47b-119">`IHttpRequestIdentifierFeature` Agrega una propiedad que se puede implementar para distinguir solicitudes de forma única.</span><span class="sxs-lookup"><span data-stu-id="0a47b-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="0a47b-120">`ISessionFeature` Define abstracciones `ISessionFactory` e `ISession` para admitir sesiones de usuario.</span><span class="sxs-lookup"><span data-stu-id="0a47b-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="0a47b-121">`ITlsConnectionFeature` Define una API para recuperar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="0a47b-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="0a47b-122">`ITlsTokenBindingFeature` Define métodos para trabajar con parámetros de enlace de tokens de TLS.</span><span class="sxs-lookup"><span data-stu-id="0a47b-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="0a47b-123">`ISessionFeature` no es una característica de servidor, pero se implementa por medio de `SessionMiddleware`. Vea [Introduction to session and application state in ASP.NET Core](app-state.md) (Introducción al estado de sesión y la aplicación de ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="0a47b-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="0a47b-124">Colecciones de características</span><span class="sxs-lookup"><span data-stu-id="0a47b-124">Feature collections</span></span>

<span data-ttu-id="0a47b-125">La propiedad `Features` de `HttpContext` proporciona una interfaz para obtener y establecer las características HTTP disponibles para la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="0a47b-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="0a47b-126">Puesto que la colección de características es mutable incluso en el contexto de una solicitud, se puede usar middleware para modificarla y para agregar compatibilidad con más características.</span><span class="sxs-lookup"><span data-stu-id="0a47b-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="0a47b-127">Middleware y características de solicitud</span><span class="sxs-lookup"><span data-stu-id="0a47b-127">Middleware and request features</span></span>

<span data-ttu-id="0a47b-128">Los servidores se encargan de crear la colección de características; el middleware, por su parte, puede agregarse a esta colección y usar las características de dicha colección.</span><span class="sxs-lookup"><span data-stu-id="0a47b-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="0a47b-129">Por ejemplo, `StaticFileMiddleware` tiene acceso a la característica `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="0a47b-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="0a47b-130">Si la característica existe, se usa para enviar el archivo estático solicitado desde la ruta de acceso física correspondiente.</span><span class="sxs-lookup"><span data-stu-id="0a47b-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="0a47b-131">Si no, se usará otro método más lento para enviarlo.</span><span class="sxs-lookup"><span data-stu-id="0a47b-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="0a47b-132">Si está disponible, `IHttpSendFileFeature` permite al sistema operativo abrir el archivo y realizar una copia directa en modo kernel en la tarjeta de red.</span><span class="sxs-lookup"><span data-stu-id="0a47b-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="0a47b-133">Además, se puede agregar middleware a la colección de características establecida por el servidor.</span><span class="sxs-lookup"><span data-stu-id="0a47b-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="0a47b-134">De hecho, las características existentes pueden incluso reemplazarse por el middleware, lo que permite que este aumente la funcionalidad del servidor.</span><span class="sxs-lookup"><span data-stu-id="0a47b-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="0a47b-135">Las características agregadas a la colección están disponibles de inmediato para otros middleware, o bien para la propia aplicación subyacente más adelante en la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="0a47b-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="0a47b-136">Al combinar implementaciones de servidor personalizadas y mejoras de middleware específicas, se puede construir el conjunto preciso de características que una aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="0a47b-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="0a47b-137">Gracias a esto, se pueden agregar las características que faltan sin necesidad de cambiar nada en el servidor y, asimismo, se garantiza que solo quede expuesta la cantidad mínima de características, lo que reduce el área de superficie de ataques y dispara el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="0a47b-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="0a47b-138">Resumen</span><span class="sxs-lookup"><span data-stu-id="0a47b-138">Summary</span></span>

<span data-ttu-id="0a47b-139">Las interfaces de características definen las características HTTP concretas que una solicitud determinada puede admitir.</span><span class="sxs-lookup"><span data-stu-id="0a47b-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="0a47b-140">Los servidores definen colecciones de características y el conjunto inicial de características admitidas por esos servidores, pero el middleware puede servir para mejorar estas características.</span><span class="sxs-lookup"><span data-stu-id="0a47b-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a47b-141">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0a47b-141">Additional resources</span></span>

* [<span data-ttu-id="0a47b-142">Servidores</span><span class="sxs-lookup"><span data-stu-id="0a47b-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="0a47b-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="0a47b-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="0a47b-144">Apertura de la interfaz web para .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="0a47b-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
