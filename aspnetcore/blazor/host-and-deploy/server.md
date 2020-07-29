---
title: Hospedaje e implementación de ASP.NET Core [Blazor Server
author: guardrex
description: Aprenda a hospedar e implementar una aplicación [Blazor Server mediante ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/host-and-deploy/server
ms.openlocfilehash: e59579046ecbfdbb4cca79bfb0e39d299e26913c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402603"
---
# <a name="host-and-deploy-blazor-server"></a>Hospedaje e implementación de [Blazor Server

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuración de host

[Las aplicaciones [Blazor Server](xref:blazor/hosting-models#blazor-server) pueden aceptar [valores de configuración de host genérico](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implementación

Con el [modelo de hospedaje de [Blazor Server](xref:blazor/hosting-models#blazor-server), [Blazor se ejecuta en el servidor desde una aplicación ASP.NET Core. Las actualizaciones de la interfaz de usuario, el control de eventos y las llamadas de JavaScript se controlan mediante una conexión [[SignalR](xref:signalr/introduction).

Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core. Visual Studio incluye la **plantilla de proyecto Aplicación [Blazor Server** (plantilla `blazorserverside` cuando se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)).

## <a name="scalability"></a>Escalabilidad

Planee una implementación para hacer el mejor uso de la infraestructura disponible para una aplicación [Blazor Server. Consulte los siguientes recursos para abordar la escalabilidad de las aplicaciones [Blazor Server:

* [Fundamentos de las aplicaciones[Blazor Server](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Servidor de implementación

A la hora de considerar la escalabilidad de un solo servidor (escalado vertical), la memoria disponible para una aplicación es probablemente el primer recurso que la aplicación agotará a medida que la demanda de los usuarios aumente. La memoria disponible en el servidor afecta a lo siguiente:

* Número de circuitos activos que un servidor puede admitir.
* Latencia de la interfaz de usuario en el cliente.

Para obtener instrucciones sobre la creación de aplicaciones [Blazor Server seguras y escalables, consulte <xref:blazor/security/server/threat-mitigation>.

Cada circuito utiliza aproximadamente 250 KB de memoria para una aplicación mínima de estilo *Hola mundo*. El tamaño de un circuito depende del código de la aplicación y de los requisitos de mantenimiento del estado asociados a cada componente. Se recomienda que mida la demanda de recursos durante el desarrollo de la aplicación y la infraestructura, pero la línea de base siguiente puede ser un punto de partida para planear el destino de implementación: Si espera que la aplicación admita 5000 usuarios simultáneos, considere la posibilidad de presupuestar al menos 1,3 GB de memoria del servidor en la aplicación (o ~273 KB por usuario).

### <a name="signalr-configuration"></a>Configuración de [SignalR

Las aplicaciones [Blazor Server usan ASP.NET Core [SignalR para comunicarse con el explorador. Las [condiciones de hospedaje y escalabilidad de [SignalR](xref:signalr/publish-to-azure-web-app) se aplican a las aplicaciones [Blazor Server.

[Blazor funciona mejor cuando se usa WebSockets como transporte de [SignalR debido a su menor latencia, confiabilidad y [seguridad](xref:signalr/security). [SignalR usa el sondeo largo cuando WebSockets no está disponible o cuando la aplicación está configurada explícitamente para usarlo. Al implementar en Azure App Service, configure la aplicación para usar WebSockets en la configuración de Azure Portal del servicio. Para más información sobre la configuración de la aplicación para Azure App Service, consulte las [[SignalRdirectrices de publicación de](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Azure [SignalR Service

Se recomienda usar [Azure [SignalR Service](/azure/azure-signalr) para las aplicaciones [Blazor Server. El servicio permite el escalado vertical de una aplicación [Blazor Server a un gran número de conexiones [SignalR simultáneas. Además, los centros de datos de alto rendimiento y alcance global del servicio [SignalR son de gran ayuda a la hora de reducir la latencia ocasionada por la geografía. Para configurar una aplicación (y, opcionalmente, aprovisionarla) Azure [SignalR Service, realice estos pasos:

1. Habilite el servicio para que admita las *sesiones permanentes*, en las que se [devuelve a los clientes al mismo servidor durante la representación previa](xref:blazor/hosting-models#connection-to-the-server). Establezca la opción `ServerStickyMode` o el valor de configuración en `Required`. Normalmente, una aplicación crea la configuración mediante **uno** de los enfoques siguientes:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.[SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuración (use **uno** de los enfoques siguientes):
  
     * `appsettings.json`:

       ```json
       "Azure:[SignalR:ServerStickyMode": "Required"
       ```

     * El valor **Configuración** > **Configuración de la aplicación** de la instancia de App Service en Azure Portal (**Nombre**: `Azure:[SignalR:ServerStickyMode`, **Valor**: `Required`).

1. Cree un perfil de publicación de aplicaciones de Azure en Visual Studio para la aplicación [Blazor Server.
1. Agregue la dependencia de **Azure [SignalR Service** al perfil. Si la suscripción de Azure no tiene una instancia de Azure [SignalR Service para asignarla a la aplicación, seleccione **Crear una instancia de Azure [SignalR Service** para aprovisionar una nueva instancia de servicio.
1. Publicación de la aplicación en Azure.

#### <a name="iis"></a>IIS

Al usar IIS, habilite:

* [WebSockets en IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sesiones temporales con Enrutamiento de solicitud de aplicaciones](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Cree una definición de entrada con las siguientes [anotaciones de Kubernetes para sesiones permanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

#### <a name="linux-with-nginx"></a>Linux con Nginx

Para que los WebSockets de [SignalR funcionen correctamente, confirme que los encabezados `Upgrade` y `Connection` del proxy estén establecidos en los valores siguientes y que `$connection_upgrade` esté asignado a uno de los siguientes elementos:

* Valor del encabezado Upgrade de forma predeterminada.
* `close` cuando falte o esté vacío el encabezado Upgrade.

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

Para obtener más información, vea los artículos siguientes:

* [NGINX como proxy de WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Redirección mediante proxy de Websocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux con Apache

Para hospedar una aplicación de [Blazor en Apache en Linux, configure `ProxyPass` para el tráfico HTTP y WebSockets.

En el ejemplo siguiente:

* El servidor de Kestrel se está ejecutando en el equipo host.
* La aplicación escucha el tráfico en el puerto 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Habilite los siguientes módulos:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Busque errores de WebSockets en la consola del explorador. Errores de ejemplo:

* Firefox no puede establecer una conexión con el servidor en ws://nombre-del-dominio.tld/_blazor?id=XXX.
* Error: No se pudo iniciar el transporte 'WebSockets': Error: Error en el transporte.
* Error: No se pudo iniciar el transporte 'LongPolling': Tipo de error: this.transport no definido
* Error: No se puede conectar con el servidor con ninguno de los transportes disponibles. Error de WebSockets
* Error: No se pueden enviar datos si la conexión no está en estado 'Conectado'.

Para más información, vea la [documentación de Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Medición de la latencia de red

[La interoperabilidad de JS](xref:blazor/call-javascript-from-dotnet) se puede usar para medir la latencia de red, como se muestra en el ejemplo siguiente:

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

Para disfrutar de una experiencia de interfaz de usuario razonable, se recomienda una latencia de interfaz de usuario sostenida de 250 ms o menos.
