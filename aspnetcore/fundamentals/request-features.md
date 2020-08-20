---
title: Características de solicitud de ASP.NET Core
author: ardalis
description: Obtenga información sobre los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas que se definen en las interfaces de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: 3b5c929519407de5dc582c10a86745efddc8a38a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634519"
---
# <a name="request-features-in-aspnet-core"></a>Características de solicitud de ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

Los detalles de implementación del servidor web relacionados con las solicitudes HTTP y las respuestas se definen en las interfaces. Estas interfaces se usan en implementaciones de servidor web y middleware para crear y modificar la canalización de hospedaje de la aplicación.

## <a name="feature-interfaces"></a>Interfaces de características

ASP.NET Core define una serie de interfaces de características HTTP en `Microsoft.AspNetCore.Http.Features` que los servidores usan para identificar las características que admiten. Las siguientes interfaces de características controlan las solicitudes y devuelven respuestas:

`IHttpRequestFeature` Define la estructura de una solicitud HTTP; esto es, el protocolo, la ruta de acceso, la cadena de consulta, los encabezados y el cuerpo.

`IHttpResponseFeature` Define la estructura de una respuesta HTTP; esto es, el código de estado, los encabezados y el cuerpo de la respuesta.

`IHttpAuthenticationFeature` Define la capacidad para identificar usuarios según un `ClaimsPrincipal` y para especificar un controlador de autenticación.

`IHttpUpgradeFeature` Define la compatibilidad con [actualizaciones HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permiten al cliente especificar otros protocolos que le gustaría usar en caso de que el servidor cambie de protocolo.

`IHttpBufferingFeature` Define métodos para deshabilitar el almacenamiento en búfer de solicitudes o respuestas.

`IHttpConnectionFeature` Define las propiedades de las direcciones y los puertos tanto locales como remotos.

`IHttpRequestLifetimeFeature` Define la capacidad para anular conexiones o para detectar si una solicitud ha finalizado antes de tiempo (por ejemplo, debido a una desconexión del cliente).

`IHttpSendFileFeature` Define un método para enviar archivos de forma asincrónica.

`IHttpWebSocketFeature` Define una API para admitir sockets web.

`IHttpRequestIdentifierFeature` Agrega una propiedad que se puede implementar para distinguir solicitudes de forma única.

`ISessionFeature` Define abstracciones `ISessionFactory` e `ISession` para admitir sesiones de usuario.

`ITlsConnectionFeature` Define una API para recuperar certificados de cliente.

`ITlsTokenBindingFeature` Define métodos para trabajar con parámetros de enlace de tokens de TLS.

> [!NOTE]
> `ISessionFeature` no es una característica de servidor, pero se implementa por medio de `SessionMiddleware`. Vea [Introduction to session and application state in ASP.NET Core](app-state.md) (Introducción al estado de sesión y la aplicación de ASP.NET Core).

## <a name="feature-collections"></a>Colecciones de características

La propiedad `Features` de `HttpContext` proporciona una interfaz para obtener y establecer las características HTTP disponibles para la solicitud actual. Puesto que la colección de características es mutable incluso en el contexto de una solicitud, se puede usar middleware para modificarla y para agregar compatibilidad con más características.

## <a name="middleware-and-request-features"></a>Middleware y características de solicitud

Los servidores se encargan de crear la colección de características; el middleware, por su parte, puede agregarse a esta colección y usar las características de dicha colección. Por ejemplo, `StaticFileMiddleware` tiene acceso a la característica `IHttpSendFileFeature`. Si la característica existe, se usa para enviar el archivo estático solicitado desde la ruta de acceso física correspondiente. Si no, se usará otro método más lento para enviarlo. Si está disponible, `IHttpSendFileFeature` permite al sistema operativo abrir el archivo y realizar una copia directa en modo kernel en la tarjeta de red.

Además, se puede agregar middleware a la colección de características establecida por el servidor. De hecho, las características existentes pueden incluso reemplazarse por el middleware, lo que permite que este aumente la funcionalidad del servidor. Las características agregadas a la colección están disponibles de inmediato para otros middleware, o bien para la propia aplicación subyacente más adelante en la canalización de solicitudes.

Al combinar implementaciones de servidor personalizadas y mejoras de middleware específicas, se puede construir el conjunto preciso de características que una aplicación necesita. Gracias a esto, se pueden agregar las características que faltan sin necesidad de cambiar nada en el servidor y, asimismo, se garantiza que solo quede expuesta la cantidad mínima de características, lo que reduce el área de superficie de ataques y dispara el rendimiento.

## <a name="summary"></a>Resumen

Las interfaces de características definen las características HTTP concretas que una solicitud determinada puede admitir. Los servidores definen colecciones de características y el conjunto inicial de características admitidas por esos servidores, pero el middleware puede servir para mejorar estas características.

## <a name="additional-resources"></a>Recursos adicionales

* [Servidores](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Apertura de la interfaz web para .NET (OWIN)](xref:fundamentals/owin)
