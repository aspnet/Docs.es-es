---
title: Hospedaje e implementación de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a hospedar e implementar una aplicación Blazor Server mediante ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: e7c8627cd27fd30288b4bcfa1ac2ffe3e9b46e29
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014222"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="a1819-103">Hospedaje e implementación de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a1819-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="a1819-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a1819-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a1819-105">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="a1819-105">Host configuration values</span></span>

<span data-ttu-id="a1819-106">[Las aplicaciones Blazor Server](xref:blazor/hosting-models#blazor-server) pueden aceptar [valores de configuración de host genérico](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="a1819-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="a1819-107">Implementación</span><span class="sxs-lookup"><span data-stu-id="a1819-107">Deployment</span></span>

<span data-ttu-id="a1819-108">Con el [modelo de hospedaje de Blazor Server](xref:blazor/hosting-models#blazor-server), Blazor se ejecuta en el servidor desde una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1819-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="a1819-109">Las actualizaciones de la interfaz de usuario, el control de eventos y las llamadas de JavaScript se controlan mediante una conexión [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="a1819-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="a1819-110">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1819-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="a1819-111">Visual Studio incluye la **plantilla de proyecto Aplicación Blazor Server** (plantilla `blazorserverside` cuando se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a1819-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="a1819-112">Escalabilidad</span><span class="sxs-lookup"><span data-stu-id="a1819-112">Scalability</span></span>

<span data-ttu-id="a1819-113">Planee una implementación para hacer el mejor uso de la infraestructura disponible para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a1819-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="a1819-114">Consulte los siguientes recursos para abordar la escalabilidad de las aplicaciones Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="a1819-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="a1819-115">Fundamentos de las aplicacionesBlazor Server</span><span class="sxs-lookup"><span data-stu-id="a1819-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="a1819-116">Servidor de implementación</span><span class="sxs-lookup"><span data-stu-id="a1819-116">Deployment server</span></span>

<span data-ttu-id="a1819-117">A la hora de considerar la escalabilidad de un solo servidor (escalado vertical), la memoria disponible para una aplicación es probablemente el primer recurso que la aplicación agotará a medida que la demanda de los usuarios aumente.</span><span class="sxs-lookup"><span data-stu-id="a1819-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="a1819-118">La memoria disponible en el servidor afecta a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a1819-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="a1819-119">Número de circuitos activos que un servidor puede admitir.</span><span class="sxs-lookup"><span data-stu-id="a1819-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="a1819-120">Latencia de la interfaz de usuario en el cliente.</span><span class="sxs-lookup"><span data-stu-id="a1819-120">UI latency on the client.</span></span>

<span data-ttu-id="a1819-121">Para obtener instrucciones sobre la creación de aplicaciones Blazor Server seguras y escalables, consulte <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="a1819-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="a1819-122">Cada circuito utiliza aproximadamente 250 KB de memoria para una aplicación mínima de estilo *Hola mundo*.</span><span class="sxs-lookup"><span data-stu-id="a1819-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="a1819-123">El tamaño de un circuito depende del código de la aplicación y de los requisitos de mantenimiento del estado asociados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="a1819-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="a1819-124">Se recomienda que mida la demanda de recursos durante el desarrollo de la aplicación y la infraestructura, pero la línea de base siguiente puede ser un punto de partida para planear el destino de implementación: Si espera que la aplicación admita 5000 usuarios simultáneos, considere la posibilidad de presupuestar al menos 1,3 GB de memoria del servidor en la aplicación (o ~273 KB por usuario).</span><span class="sxs-lookup"><span data-stu-id="a1819-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="a1819-125">Configuración de SignalR</span><span class="sxs-lookup"><span data-stu-id="a1819-125">SignalR configuration</span></span>

<span data-ttu-id="a1819-126">Las aplicaciones Blazor Server usan ASP.NET Core SignalR para comunicarse con el explorador.</span><span class="sxs-lookup"><span data-stu-id="a1819-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="a1819-127">Las [condiciones de hospedaje y escalabilidad de SignalR](xref:signalr/publish-to-azure-web-app) se aplican a las aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a1819-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="a1819-128">Blazor funciona mejor cuando se usa WebSockets como transporte de SignalR debido a su menor latencia, confiabilidad y [seguridad](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="a1819-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="a1819-129">SignalR usa el sondeo largo cuando WebSockets no está disponible o cuando la aplicación está configurada explícitamente para usarlo.</span><span class="sxs-lookup"><span data-stu-id="a1819-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="a1819-130">Al implementar en Azure App Service, configure la aplicación para usar WebSockets en la configuración de Azure Portal del servicio.</span><span class="sxs-lookup"><span data-stu-id="a1819-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="a1819-131">Para más información sobre la configuración de la aplicación para Azure App Service, consulte las [SignalRdirectrices de publicación de](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="a1819-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="a1819-132">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="a1819-132">Azure SignalR Service</span></span>

<span data-ttu-id="a1819-133">Se recomienda usar [Azure SignalR Service](/azure/azure-signalr) para las aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a1819-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="a1819-134">El servicio permite el escalado vertical de una aplicación Blazor Server a un gran número de conexiones SignalR simultáneas.</span><span class="sxs-lookup"><span data-stu-id="a1819-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="a1819-135">Además, los centros de datos de alto rendimiento y alcance global del servicio SignalR son de gran ayuda a la hora de reducir la latencia ocasionada por la geografía.</span><span class="sxs-lookup"><span data-stu-id="a1819-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="a1819-136">Para configurar una aplicación (y, opcionalmente, aprovisionarla) Azure SignalR Service, realice estos pasos:</span><span class="sxs-lookup"><span data-stu-id="a1819-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="a1819-137">Habilite el servicio para que admita las *sesiones permanentes*, en las que se [devuelve a los clientes al mismo servidor durante la representación previa](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="a1819-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="a1819-138">Establezca la opción `ServerStickyMode` o el valor de configuración en `Required`.</span><span class="sxs-lookup"><span data-stu-id="a1819-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="a1819-139">Normalmente, una aplicación crea la configuración mediante **uno** de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="a1819-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="a1819-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1819-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="a1819-141">Configuración (use **uno** de los enfoques siguientes):</span><span class="sxs-lookup"><span data-stu-id="a1819-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="a1819-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="a1819-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="a1819-143">El valor **Configuración** > **Configuración de la aplicación** de la instancia de App Service en Azure Portal (**Nombre**: `Azure:SignalR:ServerStickyMode`, **Valor**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="a1819-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="a1819-144">Cree un perfil de publicación de aplicaciones de Azure en Visual Studio para la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a1819-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="a1819-145">Agregue la dependencia de **Azure SignalR Service** al perfil.</span><span class="sxs-lookup"><span data-stu-id="a1819-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="a1819-146">Si la suscripción de Azure no tiene una instancia de Azure SignalR Service para asignarla a la aplicación, seleccione **Crear una instancia de Azure SignalR Service** para aprovisionar una nueva instancia de servicio.</span><span class="sxs-lookup"><span data-stu-id="a1819-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="a1819-147">Publicación de la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="a1819-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="a1819-148">IIS</span><span class="sxs-lookup"><span data-stu-id="a1819-148">IIS</span></span>

<span data-ttu-id="a1819-149">Al usar IIS, habilite:</span><span class="sxs-lookup"><span data-stu-id="a1819-149">When using IIS, enable:</span></span>

* <span data-ttu-id="a1819-150">[WebSockets en IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="a1819-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="a1819-151">[Sesiones temporales con Enrutamiento de solicitud de aplicaciones](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="a1819-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="a1819-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a1819-152">Kubernetes</span></span>

<span data-ttu-id="a1819-153">Cree una definición de entrada con las siguientes [anotaciones de Kubernetes para sesiones permanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="a1819-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="a1819-154">Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="a1819-154">Linux with Nginx</span></span>

<span data-ttu-id="a1819-155">Para que los WebSockets de SignalR funcionen correctamente, confirme que los encabezados `Upgrade` y `Connection` del proxy estén establecidos en los valores siguientes y que `$connection_upgrade` esté asignado a uno de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="a1819-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="a1819-156">Valor del encabezado Upgrade de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a1819-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="a1819-157">`close` cuando falte o esté vacío el encabezado Upgrade.</span><span class="sxs-lookup"><span data-stu-id="a1819-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="a1819-158">Para obtener más información, vea los artículos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a1819-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="a1819-159">NGINX como proxy de WebSocket</span><span class="sxs-lookup"><span data-stu-id="a1819-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="a1819-160">Redirección mediante proxy de Websocket</span><span class="sxs-lookup"><span data-stu-id="a1819-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="a1819-161">Linux con Apache</span><span class="sxs-lookup"><span data-stu-id="a1819-161">Linux with Apache</span></span>

<span data-ttu-id="a1819-162">Para hospedar una aplicación de Blazor en Apache en Linux, configure `ProxyPass` para el tráfico HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a1819-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="a1819-163">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a1819-163">In the following example:</span></span>

* <span data-ttu-id="a1819-164">El servidor de Kestrel se está ejecutando en el equipo host.</span><span class="sxs-lookup"><span data-stu-id="a1819-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="a1819-165">La aplicación escucha el tráfico en el puerto 5000.</span><span class="sxs-lookup"><span data-stu-id="a1819-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="a1819-166">Habilite los siguientes módulos:</span><span class="sxs-lookup"><span data-stu-id="a1819-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="a1819-167">Busque errores de WebSockets en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="a1819-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="a1819-168">Errores de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a1819-168">Example errors:</span></span>

* <span data-ttu-id="a1819-169">Firefox no puede establecer una conexión con el servidor en ws://nombre-del-dominio.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="a1819-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="a1819-170">Error: No se pudo iniciar el transporte 'WebSockets': Error: Error en el transporte.</span><span class="sxs-lookup"><span data-stu-id="a1819-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="a1819-171">Error: No se pudo iniciar el transporte 'LongPolling': Tipo de error: this.transport no definido</span><span class="sxs-lookup"><span data-stu-id="a1819-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="a1819-172">Error: No se puede conectar con el servidor con ninguno de los transportes disponibles.</span><span class="sxs-lookup"><span data-stu-id="a1819-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="a1819-173">Error de WebSockets</span><span class="sxs-lookup"><span data-stu-id="a1819-173">WebSockets failed</span></span>
* <span data-ttu-id="a1819-174">Error: No se pueden enviar datos si la conexión no está en estado 'Conectado'.</span><span class="sxs-lookup"><span data-stu-id="a1819-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="a1819-175">Para más información, vea la [documentación de Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="a1819-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="a1819-176">Medición de la latencia de red</span><span class="sxs-lookup"><span data-stu-id="a1819-176">Measure network latency</span></span>

<span data-ttu-id="a1819-177">[La interoperabilidad de JS](xref:blazor/call-javascript-from-dotnet) se puede usar para medir la latencia de red, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a1819-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="a1819-178">Para disfrutar de una experiencia de interfaz de usuario razonable, se recomienda una latencia de interfaz de usuario sostenida de 250 ms o menos.</span><span class="sxs-lookup"><span data-stu-id="a1819-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
