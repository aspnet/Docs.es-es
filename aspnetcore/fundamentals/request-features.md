---
title: Características de solicitud de ASP.NET Core
author: ardalis
description: Obtenga información sobre los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas que se definen en las interfaces de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870430"
---
# <a name="request-features-in-aspnet-core"></a>Características de solicitud de ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

La API `HttpContext` que las aplicaciones y el middleware usan para procesar solicitudes tiene una capa de abstracción debajo de ella llamada *interfaces de características*. Cada interfaz de características proporciona un subconjunto granular de la funcionalidad expuesta por `HttpContext`. El servidor o el middleware pueden agregar, modificar, ajustar, reemplazar o incluso quitar estas interfaces a medida que se procesa la solicitud sin tener que volver a implementar `HttpContext` íntegramente. También se pueden usar para simular la funcionalidad durante las pruebas.

## <a name="feature-collections"></a>Colecciones de características

La propiedad <xref:Microsoft.AspNetCore.Http.HttpContext.Features> de `HttpContext` proporciona acceso a la colección de interfaces de características para la solicitud actual. Puesto que la colección de características es mutable incluso en el contexto de una solicitud, se puede usar middleware para modificarla y para agregar compatibilidad con más características. Algunas características avanzadas solo están disponibles al acceder a la interfaz asociada a través de la colección de características.

## <a name="feature-interfaces"></a>Interfaces de características

ASP.NET Core define una serie de interfaces de características HTTP comunes en <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, que diferentes servidores y el middleware comparten para identificar las características que admiten. Los servidores y el middleware también pueden proporcionar sus propias interfaces con funcionalidad adicional.

La mayoría de las interfaces de características proporcionan una funcionalidad opcional y ligera, y sus API `HttpContext` asociadas proporcionan valores predeterminados si la característica no existe. Algunas interfaces se indican en el siguiente contenido según sea necesario, ya que proporcionan la funcionalidad de solicitud y respuesta básica y deben implementarse para procesar la solicitud.

Las siguientes interfaces de características son de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: define la estructura de una solicitud HTTP; es decir, el protocolo, la ruta de acceso, la cadena de consulta, los encabezados y el cuerpo. Esta característica es necesaria para procesar las solicitudes.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: define la estructura de una respuesta HTTP; es decir, el código de estado, los encabezados y el cuerpo de la respuesta. Esta característica es necesaria para procesar las solicitudes.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: define diferentes formas de escribir el cuerpo de la respuesta, mediante una clase `Stream` o `PipeWriter`, o un archivo. Esta característica es necesaria para procesar las solicitudes. Reemplaza `IHttpResponseFeature.Body` y `IHttpSendFileFeature`.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: contiene la clase <xref:System.Security.Claims.ClaimsPrincipal> asociada actualmente con la solicitud.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: se usa para analizar y almacenar en caché los envíos de formularios HTTP y de varias partes entrantes.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: se utiliza para controlar si se permiten las operaciones de E/S sincrónicas para los cuerpos de solicitud o respuesta.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: define métodos para deshabilitar el almacenamiento en búfer de solicitudes o respuestas.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: define las propiedades del identificador de conexión y las direcciones y los puertos tanto locales como remotos.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: controla el tamaño máximo permitido del cuerpo de la solicitud para la solicitud actual.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: indica si la solicitud puede tener un cuerpo.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: agrega una propiedad que se puede implementar para distinguir solicitudes de forma única.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: define la capacidad para anular conexiones o para detectar si una solicitud ha finalizado antes de tiempo (por ejemplo, debido a una desconexión del cliente).

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: proporciona acceso a los encabezados del finalizador de la solicitud, si existen.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: se usa para enviar mensajes de restablecimiento para los protocolos que los admiten, como HTTP/2 o HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: permite que la aplicación proporcione encabezados del finalizador de respuesta si se admiten.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: define un método para enviar archivos de forma asincrónica.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: define la compatibilidad con [actualizaciones HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permiten al cliente especificar otros protocolos que le gustaría usar en caso de que el servidor cambie de protocolo.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: define una API para admitir sockets web.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: controla si se debe usar la compresión de respuesta a través de conexiones HTTPS.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: almacena la colección de <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> para cada estado de la aplicación de solicitud.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: analiza y almacena en caché la cadena de consulta.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: representa el cuerpo de la solicitud como una clase <xref:System.IO.Pipelines.PipeReader>.
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: analiza y almacena en caché los valores del encabezado `Cookie` de solicitud.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: controla cómo las cookies de respuesta se aplican al encabezado `Set-Cookie`.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: esta característica proporciona acceso a las variables del servidor de solicitudes, como las proporcionadas por IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: proporciona acceso a una clase <xref:System.IServiceProvider> mediante servicios de solicitud con ámbito.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: define abstracciones `ISessionFactory` y <xref:Microsoft.AspNetCore.Http.ISession> para admitir sesiones de usuario. `ISessionFeature` se implementa mediante <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (vea <xref:fundamentals/app-state>).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: define una API para recuperar certificados de cliente.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: define métodos para trabajar con parámetros de enlace de tokens de TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: se usa para consultar, conceder y retirar el consentimiento del usuario en relación con el almacenamiento de la información de usuario relacionada con la actividad y la funcionalidad del sitio.
   
::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
