---
title: Características de solicitud de ASP.NET Core
author: ardalis
description: Obtenga información sobre los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas que se definen en las interfaces de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053714"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="71c5d-103">Características de solicitud de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71c5d-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="71c5d-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="71c5d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="71c5d-105">La API `HttpContext` que las aplicaciones y el middleware usan para procesar solicitudes tiene una capa de abstracción debajo de ella llamada *interfaces de características*.</span><span class="sxs-lookup"><span data-stu-id="71c5d-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces*.</span></span> <span data-ttu-id="71c5d-106">Cada interfaz de características proporciona un subconjunto granular de la funcionalidad expuesta por `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="71c5d-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="71c5d-107">El servidor o el middleware pueden agregar, modificar, ajustar, reemplazar o incluso quitar estas interfaces a medida que se procesa la solicitud sin tener que volver a implementar `HttpContext` íntegramente.</span><span class="sxs-lookup"><span data-stu-id="71c5d-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="71c5d-108">También se pueden usar para simular la funcionalidad durante las pruebas.</span><span class="sxs-lookup"><span data-stu-id="71c5d-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="71c5d-109">Colecciones de características</span><span class="sxs-lookup"><span data-stu-id="71c5d-109">Feature collections</span></span>

<span data-ttu-id="71c5d-110">La propiedad <xref:Microsoft.AspNetCore.Http.HttpContext.Features> de `HttpContext` proporciona acceso a la colección de interfaces de características para la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="71c5d-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="71c5d-111">Puesto que la colección de características es mutable incluso en el contexto de una solicitud, se puede usar middleware para modificarla y para agregar compatibilidad con más características.</span><span class="sxs-lookup"><span data-stu-id="71c5d-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="71c5d-112">Algunas características avanzadas solo están disponibles al acceder a la interfaz asociada a través de la colección de características.</span><span class="sxs-lookup"><span data-stu-id="71c5d-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="71c5d-113">Interfaces de características</span><span class="sxs-lookup"><span data-stu-id="71c5d-113">Feature interfaces</span></span>

<span data-ttu-id="71c5d-114">ASP.NET Core define una serie de interfaces de características HTTP comunes en <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, que diferentes servidores y el middleware comparten para identificar las características que admiten.</span><span class="sxs-lookup"><span data-stu-id="71c5d-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="71c5d-115">Los servidores y el middleware también pueden proporcionar sus propias interfaces con funcionalidad adicional.</span><span class="sxs-lookup"><span data-stu-id="71c5d-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="71c5d-116">La mayoría de las interfaces de características proporcionan una funcionalidad opcional y ligera, y sus API `HttpContext` asociadas proporcionan valores predeterminados si la característica no existe.</span><span class="sxs-lookup"><span data-stu-id="71c5d-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="71c5d-117">Algunas interfaces se indican en el siguiente contenido según sea necesario, ya que proporcionan la funcionalidad de solicitud y respuesta básica y deben implementarse para procesar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="71c5d-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="71c5d-118">Las siguientes interfaces de características son de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="71c5d-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="71c5d-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: define la estructura de una solicitud HTTP; es decir, el protocolo, la ruta de acceso, la cadena de consulta, los encabezados y el cuerpo.</span><span class="sxs-lookup"><span data-stu-id="71c5d-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="71c5d-120">Esta característica es necesaria para procesar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="71c5d-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="71c5d-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: define la estructura de una respuesta HTTP; es decir, el código de estado, los encabezados y el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="71c5d-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="71c5d-122">Esta característica es necesaria para procesar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="71c5d-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71c5d-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: define diferentes formas de escribir el cuerpo de la respuesta, mediante una clase `Stream` o `PipeWriter`, o un archivo.</span><span class="sxs-lookup"><span data-stu-id="71c5d-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="71c5d-124">Esta característica es necesaria para procesar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="71c5d-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="71c5d-125">Reemplaza `IHttpResponseFeature.Body` y `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="71c5d-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="71c5d-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: contiene la clase <xref:System.Security.Claims.ClaimsPrincipal> asociada actualmente con la solicitud.</span><span class="sxs-lookup"><span data-stu-id="71c5d-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="71c5d-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: se usa para analizar y almacenar en caché los envíos de formularios HTTP y de varias partes entrantes.</span><span class="sxs-lookup"><span data-stu-id="71c5d-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="71c5d-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: se utiliza para controlar si se permiten las operaciones de E/S sincrónicas para los cuerpos de solicitud o respuesta.</span><span class="sxs-lookup"><span data-stu-id="71c5d-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71c5d-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: define métodos para deshabilitar el almacenamiento en búfer de solicitudes o respuestas.</span><span class="sxs-lookup"><span data-stu-id="71c5d-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="71c5d-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: define las propiedades del identificador de conexión y las direcciones y los puertos tanto locales como remotos.</span><span class="sxs-lookup"><span data-stu-id="71c5d-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="71c5d-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: controla el tamaño máximo permitido del cuerpo de la solicitud para la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="71c5d-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="71c5d-132">`IHttpRequestBodyDetectionFeature`: indica si la solicitud puede tener un cuerpo.</span><span class="sxs-lookup"><span data-stu-id="71c5d-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="71c5d-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: agrega una propiedad que se puede implementar para distinguir solicitudes de forma única.</span><span class="sxs-lookup"><span data-stu-id="71c5d-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="71c5d-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: define la capacidad para anular conexiones o para detectar si una solicitud ha finalizado antes de tiempo (por ejemplo, debido a una desconexión del cliente).</span><span class="sxs-lookup"><span data-stu-id="71c5d-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71c5d-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: proporciona acceso a los encabezados del finalizador de la solicitud, si existen.</span><span class="sxs-lookup"><span data-stu-id="71c5d-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="71c5d-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: se usa para enviar mensajes de restablecimiento para los protocolos que los admiten, como HTTP/2 o HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="71c5d-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="71c5d-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: permite que la aplicación proporcione encabezados del finalizador de respuesta si se admiten.</span><span class="sxs-lookup"><span data-stu-id="71c5d-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71c5d-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: define un método para enviar archivos de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="71c5d-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="71c5d-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: define la compatibilidad con [actualizaciones HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permiten al cliente especificar otros protocolos que le gustaría usar en caso de que el servidor cambie de protocolo.</span><span class="sxs-lookup"><span data-stu-id="71c5d-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="71c5d-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: define una API para admitir sockets web.</span><span class="sxs-lookup"><span data-stu-id="71c5d-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71c5d-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: controla si se debe usar la compresión de respuesta a través de conexiones HTTPS.</span><span class="sxs-lookup"><span data-stu-id="71c5d-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="71c5d-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: almacena la colección de <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> para cada estado de la aplicación de solicitud.</span><span class="sxs-lookup"><span data-stu-id="71c5d-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="71c5d-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: analiza y almacena en caché la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="71c5d-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71c5d-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: representa el cuerpo de la solicitud como una clase <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="71c5d-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="71c5d-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: analiza y almacena en caché los valores del encabezado `:::no-loc(Cookie):::` de solicitud.</span><span class="sxs-lookup"><span data-stu-id="71c5d-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: Parses and caches the request `:::no-loc(Cookie):::` header values.</span></span>

<span data-ttu-id="71c5d-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: controla cómo las :::no-loc(cookie):::s de respuesta se aplican al encabezado `Set-:::no-loc(Cookie):::`.</span><span class="sxs-lookup"><span data-stu-id="71c5d-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: Controls how response :::no-loc(cookie):::s are applied to the `Set-:::no-loc(Cookie):::` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="71c5d-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: esta característica proporciona acceso a las variables del servidor de solicitudes, como las proporcionadas por IIS.</span><span class="sxs-lookup"><span data-stu-id="71c5d-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="71c5d-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: proporciona acceso a una clase <xref:System.IServiceProvider> mediante servicios de solicitud con ámbito.</span><span class="sxs-lookup"><span data-stu-id="71c5d-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="71c5d-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: define abstracciones `ISessionFactory` y <xref:Microsoft.AspNetCore.Http.ISession> para admitir sesiones de usuario.</span><span class="sxs-lookup"><span data-stu-id="71c5d-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="71c5d-150">`ISessionFeature` se implementa mediante <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (vea <xref:fundamentals/app-state>).</span><span class="sxs-lookup"><span data-stu-id="71c5d-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="71c5d-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: define una API para recuperar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="71c5d-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="71c5d-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: define métodos para trabajar con parámetros de enlace de tokens de TLS.</span><span class="sxs-lookup"><span data-stu-id="71c5d-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="71c5d-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: se usa para consultar, conceder y retirar el consentimiento del usuario en relación con el almacenamiento de la información de usuario relacionada con la actividad y la funcionalidad del sitio.</span><span class="sxs-lookup"><span data-stu-id="71c5d-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="71c5d-154">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="71c5d-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
