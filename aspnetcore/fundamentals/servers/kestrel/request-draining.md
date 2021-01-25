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
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Vaciado de solicitudes con el servidor web Kestrel de ASP.NET Core

La apertura de conexiones HTTP lleva mucho tiempo. En HTTPS, además consume muchos recursos. Por lo tanto, Kestrel intenta reutilizar las conexiones conforme al protocolo HTTP/1.1. El cuerpo de una solicitud debe usarse por completo para que se vuelva a usar la conexión. La aplicación no siempre consume el cuerpo de la solicitud, como las solicitudes HTTP POST en las que el servidor devuelve un redireccionamiento o una respuesta 404. En el caso de redireccionamiento de HTTP POST:

* Es posible que el cliente ya haya enviado parte de los datos de POST.
* El servidor escribe la respuesta 301.
* La conexión no se puede usar para una nueva solicitud hasta que los datos POST del cuerpo de la solicitud anterior se hayan leído en su totalidad.
* Kestrel intenta vaciar el cuerpo de la solicitud. El vaciado del cuerpo de la solicitud significa leer y descartar los datos sin procesarlos.

El proceso de vaciado llega a un equilibrio entre permitir que se vuelva a usar la conexión y el tiempo que se tarda en vaciar los datos restantes:

* El vaciado tiene un tiempo de espera de cinco segundos que no se puede configurar.
* Si no se han leído todos los datos especificados por el encabezado `Content-Length` o `Transfer-Encoding` antes de que se agote el tiempo de espera, se cierra la conexión.

A veces, puede que quiera finalizar la solicitud inmediatamente, antes o después de escribir la respuesta. Por ejemplo, los clientes pueden tener límites de datos restrictivos. La limitación de los datos cargados puede ser una prioridad. En tales casos, para finalizar una solicitud, llame a [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) desde un controlador, una página de Razor o un middleware.

Hay advertencias respecto a la llamada a `Abort`:

* La creación de nuevas conexiones puede ser lenta y costosa.
* No hay ninguna garantía de que el cliente haya leído la respuesta antes de que se cierre la conexión.
* La llamada a `Abort` debe ser poco frecuente y reservada para casos de error graves, no comunes.
  * Llame a `Abort` solo cuando sea necesario resolver un problema concreto. Por ejemplo, llame a `Abort` si hay clientes malintencionados que intentan aplicar POST a los datos, o bien si hay un error en el código cliente que genera varias solicitudes o de gran tamaño.
  * No llame a `Abort` para situaciones de error comunes, como HTTP 404 (No encontrado).

La llamada a [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes de llamar a `Abort` garantiza que el servidor haya terminado de escribir la respuesta. Pero el comportamiento del cliente no es predecible y es posible que no lea la respuesta antes de que se anule la conexión.

Este proceso es diferente para HTTP/2, ya que el protocolo admite la anulación de flujos de solicitud individuales sin cerrar la conexión. El tiempo de espera de vaciado de cinco segundos no se aplica. Si hay datos del cuerpo de la solicitud sin leer después de completar una respuesta, el servidor envía un marco RST HTTP/2. Se omiten los marcos de datos del cuerpo de la solicitud adicionales.

Si es posible, es mejor que los clientes usen el encabezado de solicitud [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) y esperen a que el servidor responda para empezar a enviar el cuerpo de la solicitud. Esto proporciona al cliente la oportunidad de examinar la respuesta y anular antes de enviar datos innecesarios.
