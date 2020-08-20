---
title: SignalRHospedaje y escalado de producción ASP.net Core
author: bradygaster
description: Obtenga información sobre cómo evitar problemas de rendimiento y escalado en aplicaciones que usan ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: fc257015a9ee972da90b0f206a60b07bd6cc1f97
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631113"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="33c29-103">SignalRHospedaje y escalado de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="33c29-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="33c29-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse), [Brady transgastor](https://twitter.com/bradygaster)y [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="33c29-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="33c29-105">En este artículo se explican las consideraciones sobre el hospedaje y el escalado para aplicaciones de tráfico elevado que usan ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="33c29-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="33c29-106">Sesiones permanentes</span><span class="sxs-lookup"><span data-stu-id="33c29-106">Sticky Sessions</span></span>

<span data-ttu-id="33c29-107">SignalR requiere que todas las solicitudes HTTP para una conexión específica se controlen mediante el mismo proceso de servidor.</span><span class="sxs-lookup"><span data-stu-id="33c29-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="33c29-108">Cuando SignalR se ejecuta en una granja de servidores (varios servidores), se deben usar "sesiones permanentes".</span><span class="sxs-lookup"><span data-stu-id="33c29-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="33c29-109">En algunos equilibradores de carga también se denominan "sesiones permanentes".</span><span class="sxs-lookup"><span data-stu-id="33c29-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="33c29-110">Azure App Service usa el [enrutamiento de solicitud de aplicaciones](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) para enrutar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="33c29-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="33c29-111">Al habilitar la configuración de "afinidad ARR" en el Azure App Service, se habilitarán las "sesiones permanentes".</span><span class="sxs-lookup"><span data-stu-id="33c29-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="33c29-112">Las únicas circunstancias en las que no se requieren sesiones permanentes son:</span><span class="sxs-lookup"><span data-stu-id="33c29-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="33c29-113">Al hospedar en un solo servidor, en un único proceso.</span><span class="sxs-lookup"><span data-stu-id="33c29-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="33c29-114">Al usar el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="33c29-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="33c29-115">Cuando todos los clientes están configurados para usar **solo** WebSockets **y** el [valor SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitado en la configuración del cliente.</span><span class="sxs-lookup"><span data-stu-id="33c29-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="33c29-116">En todas las demás circunstancias (incluso cuando se usa el backplane de Redis), el entorno de servidor debe configurarse para sesiones permanentes.</span><span class="sxs-lookup"><span data-stu-id="33c29-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="33c29-117">Para obtener instrucciones sobre la configuración de Azure App Service para SignalR , vea <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="33c29-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="33c29-118">Recursos de conexión TCP</span><span class="sxs-lookup"><span data-stu-id="33c29-118">TCP connection resources</span></span>

<span data-ttu-id="33c29-119">El número de conexiones TCP simultáneas que un servidor web puede admitir es limitado.</span><span class="sxs-lookup"><span data-stu-id="33c29-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="33c29-120">Los clientes HTTP estándar usan conexiones *efímeras* .</span><span class="sxs-lookup"><span data-stu-id="33c29-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="33c29-121">Estas conexiones se pueden cerrar cuando el cliente se queda inactivo y se vuelve a abrir posteriormente.</span><span class="sxs-lookup"><span data-stu-id="33c29-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="33c29-122">Por otro lado, una SignalR conexión es *persistente*.</span><span class="sxs-lookup"><span data-stu-id="33c29-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="33c29-123">SignalR las conexiones permanecen abiertas incluso cuando el cliente se queda inactivo.</span><span class="sxs-lookup"><span data-stu-id="33c29-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="33c29-124">En una aplicación de alto tráfico que sirve a muchos clientes, estas conexiones persistentes pueden hacer que los servidores alcancen el número máximo de conexiones.</span><span class="sxs-lookup"><span data-stu-id="33c29-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="33c29-125">Las conexiones persistentes también consumen memoria adicional, para realizar un seguimiento de cada conexión.</span><span class="sxs-lookup"><span data-stu-id="33c29-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="33c29-126">El uso intensivo de los recursos relacionados con la conexión de SignalR puede afectar a otras aplicaciones web que se hospedan en el mismo servidor.</span><span class="sxs-lookup"><span data-stu-id="33c29-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="33c29-127">Cuando SignalR se abre y contiene las últimas conexiones TCP disponibles, otras aplicaciones web en el mismo servidor tampoco tienen disponibles más conexiones.</span><span class="sxs-lookup"><span data-stu-id="33c29-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="33c29-128">Si un servidor se queda sin conexiones, verá errores de socket aleatorios y errores de restablecimiento de conexión.</span><span class="sxs-lookup"><span data-stu-id="33c29-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="33c29-129">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="33c29-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="33c29-130">Para evitar que SignalR el uso de recursos cause errores en otras aplicaciones Web, ejecute SignalR en servidores diferentes a los de otras aplicaciones Web.</span><span class="sxs-lookup"><span data-stu-id="33c29-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="33c29-131">Para evitar que SignalR el uso de recursos provoque errores en una SignalR aplicación, escale horizontalmente para limitar el número de conexiones que un servidor tiene que controlar.</span><span class="sxs-lookup"><span data-stu-id="33c29-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="33c29-132">Escalado horizontal</span><span class="sxs-lookup"><span data-stu-id="33c29-132">Scale out</span></span>

<span data-ttu-id="33c29-133">Una aplicación que usa SignalR debe realizar un seguimiento de todas sus conexiones, lo que crea problemas para una granja de servidores.</span><span class="sxs-lookup"><span data-stu-id="33c29-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="33c29-134">Agregue un servidor y obtenga las nuevas conexiones que los otros servidores no conocen.</span><span class="sxs-lookup"><span data-stu-id="33c29-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="33c29-135">Por ejemplo, en SignalR cada servidor del diagrama siguiente no es consciente de las conexiones en los otros servidores.</span><span class="sxs-lookup"><span data-stu-id="33c29-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="33c29-136">Cuando SignalR en uno de los servidores desea enviar un mensaje a todos los clientes, el mensaje solo se dirige a los clientes conectados a ese servidor.</span><span class="sxs-lookup"><span data-stu-id="33c29-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Escalado::: no-LOC (Signalr)::: sin un plano posterior](scale/_static/scale-no-backplane.png)

<span data-ttu-id="33c29-138">Las opciones para resolver este problema son el [ SignalR servicio de Azure](#azure-signalr-service) y el [backplane de Redis](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="33c29-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="33c29-139">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="33c29-139">Azure SignalR Service</span></span>

<span data-ttu-id="33c29-140">El servicio de Azure SignalR es un proxy en lugar de un backplane.</span><span class="sxs-lookup"><span data-stu-id="33c29-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="33c29-141">Cada vez que un cliente inicia una conexión al servidor, el cliente se redirige para conectarse al servicio.</span><span class="sxs-lookup"><span data-stu-id="33c29-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="33c29-142">Ese proceso se ilustra en el diagrama siguiente:</span><span class="sxs-lookup"><span data-stu-id="33c29-142">That process is illustrated in the following diagram:</span></span>

![Establecer una conexión con Azure::: no-LOC (Signalr)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="33c29-144">El resultado es que el servicio administra todas las conexiones de cliente, mientras que cada servidor solo necesita un pequeño número constante de conexiones al servicio, tal y como se muestra en el diagrama siguiente:</span><span class="sxs-lookup"><span data-stu-id="33c29-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Clientes conectados al servicio, servidores conectados al servicio](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="33c29-146">Este enfoque para el escalado horizontal tiene varias ventajas sobre la alternativa de backplane de Redis:</span><span class="sxs-lookup"><span data-stu-id="33c29-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="33c29-147">Las sesiones permanentes, también conocidas como [afinidad de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), no son necesarias, ya que los clientes se redirigen inmediatamente al servicio de Azure SignalR cuando se conectan.</span><span class="sxs-lookup"><span data-stu-id="33c29-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="33c29-148">Una SignalR aplicación puede escalarse horizontalmente en función del número de mensajes enviados, mientras que el servicio de Azure SignalR se escala automáticamente para controlar cualquier número de conexiones.</span><span class="sxs-lookup"><span data-stu-id="33c29-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="33c29-149">Por ejemplo, puede haber miles de clientes, pero si solo se envían unos pocos mensajes por segundo, la SignalR aplicación no tendrá que escalar horizontalmente a varios servidores solo para administrar las propias conexiones.</span><span class="sxs-lookup"><span data-stu-id="33c29-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="33c29-150">Una SignalR aplicación no usará significativamente más recursos de conexión que una aplicación web sin SignalR .</span><span class="sxs-lookup"><span data-stu-id="33c29-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="33c29-151">Por estas razones, se recomienda el SignalR servicio de Azure para todas ASP.net Core SignalR aplicaciones hospedadas en Azure, incluidas App Service, máquinas virtuales y contenedores.</span><span class="sxs-lookup"><span data-stu-id="33c29-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="33c29-152">Para obtener más información, consulte la [ SignalR documentación del servicio de Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="33c29-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="33c29-153">Backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="33c29-153">Redis backplane</span></span>

<span data-ttu-id="33c29-154">[Redis](https://redis.io/) es un almacén de clave-valor en memoria que admite un sistema de mensajería con un modelo de publicación/suscripción.</span><span class="sxs-lookup"><span data-stu-id="33c29-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="33c29-155">El SignalR backplane Redis usa la característica pub/sub para reenviar mensajes a otros servidores.</span><span class="sxs-lookup"><span data-stu-id="33c29-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="33c29-156">Cuando un cliente realiza una conexión, la información de conexión se pasa al backplane.</span><span class="sxs-lookup"><span data-stu-id="33c29-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="33c29-157">Cuando un servidor desea enviar un mensaje a todos los clientes, envía al plano posterior.</span><span class="sxs-lookup"><span data-stu-id="33c29-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="33c29-158">El backplane sabe todos los clientes conectados y los servidores en los que se encuentran.</span><span class="sxs-lookup"><span data-stu-id="33c29-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="33c29-159">Envía el mensaje a todos los clientes a través de sus servidores respectivos.</span><span class="sxs-lookup"><span data-stu-id="33c29-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="33c29-160">Este proceso se ilustra en el diagrama siguiente:</span><span class="sxs-lookup"><span data-stu-id="33c29-160">This process is illustrated in the following diagram:</span></span>

![Backplane de Redis, mensaje enviado desde un servidor a todos los clientes](scale/_static/redis-backplane.png)

<span data-ttu-id="33c29-162">El backplane de Redis es el enfoque de escalado horizontal recomendado para las aplicaciones hospedadas en su propia infraestructura.</span><span class="sxs-lookup"><span data-stu-id="33c29-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="33c29-163">Si hay una latencia de conexión importante entre su centro de datos y un centro de datos de Azure, SignalR es posible que el servicio de Azure no sea una opción práctica para aplicaciones locales con requisitos de baja latencia o alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="33c29-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="33c29-164">Las ventajas del servicio de Azure SignalR mencionadas anteriormente son inconvenientes para el backplane de Redis:</span><span class="sxs-lookup"><span data-stu-id="33c29-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="33c29-165">Las sesiones permanentes, también conocidas como [afinidad de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), son necesarias, excepto cuando se cumplen las **dos** condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="33c29-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="33c29-166">Todos los clientes están configurados para usar **solo** WebSockets.</span><span class="sxs-lookup"><span data-stu-id="33c29-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="33c29-167">La [opción SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada en la configuración del cliente.</span><span class="sxs-lookup"><span data-stu-id="33c29-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="33c29-168">Una vez que se inicia una conexión en un servidor, la conexión tiene que permanecer en ese servidor.</span><span class="sxs-lookup"><span data-stu-id="33c29-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="33c29-169">Una SignalR aplicación debe escalarse horizontalmente en función del número de clientes aunque se envíen pocos mensajes.</span><span class="sxs-lookup"><span data-stu-id="33c29-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="33c29-170">Una SignalR aplicación usa significativamente más recursos de conexión que una aplicación web sin SignalR .</span><span class="sxs-lookup"><span data-stu-id="33c29-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="33c29-171">Limitaciones de IIS en el sistema operativo de cliente Windows</span><span class="sxs-lookup"><span data-stu-id="33c29-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="33c29-172">Windows 10 y Windows 8. x son sistemas operativos cliente.</span><span class="sxs-lookup"><span data-stu-id="33c29-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="33c29-173">IIS en los sistemas operativos cliente tiene un límite de 10 conexiones simultáneas.</span><span class="sxs-lookup"><span data-stu-id="33c29-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="33c29-174">SignalRlas conexiones de son:</span><span class="sxs-lookup"><span data-stu-id="33c29-174">SignalR's connections are:</span></span>

* <span data-ttu-id="33c29-175">Transitorios y reestablecidos con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="33c29-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="33c29-176">**No** se elimina inmediatamente cuando ya no se usa.</span><span class="sxs-lookup"><span data-stu-id="33c29-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="33c29-177">Las condiciones anteriores hacen que sea probable que alcance el límite de 10 conexiones en un sistema operativo cliente.</span><span class="sxs-lookup"><span data-stu-id="33c29-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="33c29-178">Cuando se usa un sistema operativo de cliente para el desarrollo, se recomienda:</span><span class="sxs-lookup"><span data-stu-id="33c29-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="33c29-179">Evite IIS.</span><span class="sxs-lookup"><span data-stu-id="33c29-179">Avoid IIS.</span></span>
* <span data-ttu-id="33c29-180">Use Kestrel o IIS Express como destinos de implementación.</span><span class="sxs-lookup"><span data-stu-id="33c29-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="33c29-181">Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="33c29-181">Linux with Nginx</span></span>

<span data-ttu-id="33c29-182">Establezca los `Connection` encabezados y del proxy `Upgrade` en lo siguiente para SignalR WebSockets:</span><span class="sxs-lookup"><span data-stu-id="33c29-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="33c29-183">Para obtener más información, vea [NGINX como proxy de WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="33c29-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="33c29-184">Proveedores de backplane de terceros SignalR</span><span class="sxs-lookup"><span data-stu-id="33c29-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="33c29-185">NCache</span><span class="sxs-lookup"><span data-stu-id="33c29-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="33c29-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="33c29-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="33c29-187">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="33c29-187">Next steps</span></span>

<span data-ttu-id="33c29-188">Para obtener más información, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="33c29-188">For more information, see the following resources:</span></span>

* [<span data-ttu-id="33c29-189">Documentación del servicio de Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="33c29-189">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="33c29-190">Configuración de un backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="33c29-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
