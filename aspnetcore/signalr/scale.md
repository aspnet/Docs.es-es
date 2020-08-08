---
title: SignalRHospedaje y escalado de producción ASP.net Core
author: bradygaster
description: Obtenga información sobre cómo evitar problemas de rendimiento y escalado en aplicaciones que usan ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 2d128d54dc9b1189124563e45d72d74b19704ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022529"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a>SignalRHospedaje y escalado de ASP.net Core

Por [Andrew Stanton-enfermera](https://twitter.com/anurse), [Brady transgastor](https://twitter.com/bradygaster)y [Tom Dykstra](https://github.com/tdykstra),

En este artículo se explican las consideraciones sobre el hospedaje y el escalado para aplicaciones de tráfico elevado que usan ASP.NET Core SignalR .

## <a name="sticky-sessions"></a>Sesiones permanentes

SignalRrequiere que todas las solicitudes HTTP para una conexión específica se controlen mediante el mismo proceso de servidor. Cuando SignalR se ejecuta en una granja de servidores (varios servidores), se deben usar "sesiones permanentes". En algunos equilibradores de carga también se denominan "sesiones permanentes". Azure App Service usa el [enrutamiento de solicitud de aplicaciones](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) para enrutar las solicitudes. Al habilitar la configuración de "afinidad ARR" en el Azure App Service, se habilitarán las "sesiones permanentes". Las únicas circunstancias en las que no se requieren sesiones permanentes son:

1. Al hospedar en un solo servidor, en un único proceso.
1. Al usar el servicio de Azure SignalR .
1. Cuando todos los clientes están configurados para usar **solo** WebSockets **y** el [valor SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitado en la configuración del cliente.

En todas las demás circunstancias (incluso cuando se usa el backplane de Redis), el entorno de servidor debe configurarse para sesiones permanentes.

Para obtener instrucciones sobre la configuración de Azure App Service para SignalR , vea <xref:signalr/publish-to-azure-web-app> .

## <a name="tcp-connection-resources"></a>Recursos de conexión TCP

El número de conexiones TCP simultáneas que un servidor web puede admitir es limitado. Los clientes HTTP estándar usan conexiones *efímeras* . Estas conexiones se pueden cerrar cuando el cliente se queda inactivo y se vuelve a abrir posteriormente. Por otro lado, una SignalR conexión es *persistente*. SignalRlas conexiones permanecen abiertas incluso cuando el cliente se queda inactivo. En una aplicación de alto tráfico que sirve a muchos clientes, estas conexiones persistentes pueden hacer que los servidores alcancen el número máximo de conexiones.

Las conexiones persistentes también consumen memoria adicional, para realizar un seguimiento de cada conexión.

El uso intensivo de los recursos relacionados con la conexión de SignalR puede afectar a otras aplicaciones web que se hospedan en el mismo servidor. Cuando SignalR se abre y contiene las últimas conexiones TCP disponibles, otras aplicaciones web en el mismo servidor tampoco tienen disponibles más conexiones.

Si un servidor se queda sin conexiones, verá errores de socket aleatorios y errores de restablecimiento de conexión. Por ejemplo:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Para evitar que SignalR el uso de recursos cause errores en otras aplicaciones Web, ejecute SignalR en servidores diferentes a los de otras aplicaciones Web.

Para evitar que SignalR el uso de recursos provoque errores en una SignalR aplicación, escale horizontalmente para limitar el número de conexiones que un servidor tiene que controlar.

## <a name="scale-out"></a>Escalado horizontal

Una aplicación que usa SignalR debe realizar un seguimiento de todas sus conexiones, lo que crea problemas para una granja de servidores. Agregue un servidor y obtenga las nuevas conexiones que los otros servidores no conocen. Por ejemplo, en SignalR cada servidor del diagrama siguiente no es consciente de las conexiones en los otros servidores. Cuando SignalR en uno de los servidores desea enviar un mensaje a todos los clientes, el mensaje solo se dirige a los clientes conectados a ese servidor.

![Escalado::: no-LOC (Signalr)::: sin un plano posterior](scale/_static/scale-no-backplane.png)

Las opciones para resolver este problema son el [ SignalR servicio de Azure](#azure-signalr-service) y el [backplane de Redis](#redis-backplane).

## <a name="azure-no-locsignalr-service"></a>Azure SignalR Service

El servicio de Azure SignalR es un proxy en lugar de un backplane. Cada vez que un cliente inicia una conexión al servidor, el cliente se redirige para conectarse al servicio. Ese proceso se ilustra en el diagrama siguiente:

![Establecer una conexión con Azure::: no-LOC (Signalr)::: Service](scale/_static/azure-signalr-service-one-connection.png)

El resultado es que el servicio administra todas las conexiones de cliente, mientras que cada servidor solo necesita un pequeño número constante de conexiones al servicio, tal y como se muestra en el diagrama siguiente:

![Clientes conectados al servicio, servidores conectados al servicio](scale/_static/azure-signalr-service-multiple-connections.png)

Este enfoque para el escalado horizontal tiene varias ventajas sobre la alternativa de backplane de Redis:

* Las sesiones permanentes, también conocidas como [afinidad de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), no son necesarias, ya que los clientes se redirigen inmediatamente al servicio de Azure SignalR cuando se conectan.
* Una SignalR aplicación puede escalarse horizontalmente en función del número de mensajes enviados, mientras que el servicio de Azure SignalR se escala automáticamente para controlar cualquier número de conexiones. Por ejemplo, puede haber miles de clientes, pero si solo se envían unos pocos mensajes por segundo, la SignalR aplicación no tendrá que escalar horizontalmente a varios servidores solo para administrar las propias conexiones.
* Una SignalR aplicación no usará significativamente más recursos de conexión que una aplicación web sin SignalR .

Por estas razones, se recomienda el SignalR servicio de Azure para todas ASP.net Core SignalR aplicaciones hospedadas en Azure, incluidas App Service, máquinas virtuales y contenedores.

Para obtener más información, consulte la [ SignalR documentación del servicio de Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane de Redis

[Redis](https://redis.io/) es un almacén de clave-valor en memoria que admite un sistema de mensajería con un modelo de publicación/suscripción. El SignalR backplane Redis usa la característica pub/sub para reenviar mensajes a otros servidores. Cuando un cliente realiza una conexión, la información de conexión se pasa al backplane. Cuando un servidor desea enviar un mensaje a todos los clientes, envía al plano posterior. El backplane sabe todos los clientes conectados y los servidores en los que se encuentran. Envía el mensaje a todos los clientes a través de sus servidores respectivos. Este proceso se ilustra en el diagrama siguiente:

![Backplane de Redis, mensaje enviado desde un servidor a todos los clientes](scale/_static/redis-backplane.png)

El backplane de Redis es el enfoque de escalado horizontal recomendado para las aplicaciones hospedadas en su propia infraestructura. Si hay una latencia de conexión importante entre su centro de datos y un centro de datos de Azure, SignalR es posible que el servicio de Azure no sea una opción práctica para aplicaciones locales con requisitos de baja latencia o alto rendimiento.

Las ventajas del servicio de Azure SignalR mencionadas anteriormente son inconvenientes para el backplane de Redis:

* Las sesiones permanentes, también conocidas como [afinidad de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), son necesarias, excepto cuando se cumplen las **dos** condiciones siguientes:
  * Todos los clientes están configurados para usar **solo** WebSockets.
  * La [opción SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada en la configuración del cliente. 
   Una vez que se inicia una conexión en un servidor, la conexión tiene que permanecer en ese servidor.
* Una SignalR aplicación debe escalarse horizontalmente en función del número de clientes aunque se envíen pocos mensajes.
* Una SignalR aplicación usa significativamente más recursos de conexión que una aplicación web sin SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>Limitaciones de IIS en el sistema operativo de cliente Windows

Windows 10 y Windows 8. x son sistemas operativos cliente. IIS en los sistemas operativos cliente tiene un límite de 10 conexiones simultáneas. SignalRlas conexiones de son:

* Transitorios y reestablecidos con frecuencia.
* **No** se elimina inmediatamente cuando ya no se usa.

Las condiciones anteriores hacen que sea probable que alcance el límite de 10 conexiones en un sistema operativo cliente. Cuando se usa un sistema operativo de cliente para el desarrollo, se recomienda:

* Evite IIS.
* Use Kestrel o IIS Express como destinos de implementación.

## <a name="linux-with-nginx"></a>Linux con Nginx

Establezca los `Connection` encabezados y del proxy `Upgrade` en lo siguiente para SignalR WebSockets:

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Para obtener más información, vea [NGINX como proxy de WebSocket](https://www.nginx.com/blog/websocket-nginx/).

## <a name="third-party-no-locsignalr-backplane-providers"></a>Proveedores de backplane de terceros SignalR

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Documentación del servicio de Azure SignalR](/azure/azure-signalr/signalr-overview)
* [Configuración de un backplane de Redis](xref:signalr/redis-backplane)
