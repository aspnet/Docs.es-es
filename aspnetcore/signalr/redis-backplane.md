---
title: Backplane de Redis para SignalR el escalado horizontal de ASP.net Core
author: bradygaster
description: Aprenda a configurar un backplane de Redis para habilitar el escalado horizontal de una SignalR aplicación ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: a57176409c5f9bcc620db7e070f6616951eb9a54
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722779"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a>Configuración de un backplane de Redis para ASP.NET Core SignalR la escalabilidad horizontal

Por [Andrew Stanton-enfermera](https://twitter.com/anurse), [Brady transgastor](https://twitter.com/bradygaster)y [Tom Dykstra](https://github.com/tdykstra),

En este artículo se explican los SignalR aspectos específicos de la configuración de un servidor de [Redis](https://redis.io/) para usar para escalar horizontalmente una SignalR aplicación ASP.net Core.

## <a name="set-up-a-redis-backplane"></a>Configuración de un backplane de Redis

* Implementar un servidor de Redis.

  > [!IMPORTANT] 
  > Para su uso en producción, se recomienda un backplane de Redis solo cuando se ejecuta en el mismo centro de datos que la SignalR aplicación. De lo contrario, la latencia de red degrada el rendimiento. Si la SignalR aplicación se ejecuta en la nube de Azure, se recomienda el SignalR servicio de Azure en lugar de un backplane de Redis. Puede usar el servicio de Azure Redis Cache para entornos de desarrollo y pruebas.

  Para obtener más información, vea los siguientes recursos:

  * <xref:signalr/scale>
  * [Documentación de Redis](https://redis.io/)
  * [Documentación de Azure Redis Cache](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* En la SignalR aplicación, instale el `Microsoft.AspNetCore.SignalR.Redis` paquete NuGet.
* En el `Startup.ConfigureServices` método, llame a `AddRedis` después de `AddSignalR` :

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Configure las opciones según sea necesario:
 
  La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.

  En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto. En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* En la SignalR aplicación, instale uno de los siguientes paquetes NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Depende de StackExchange. Redis 2. X.X. Este es el paquete recomendado para ASP.NET Core 2,2 y versiones posteriores.
  * `Microsoft.AspNetCore.SignalR.Redis` -Depende de StackExchange. Redis 1. X.X. Este paquete no está incluido en ASP.NET Core 3,0 y versiones posteriores.

* En el `Startup.ConfigureServices` método, llame a <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Al utilizar `Microsoft.AspNetCore.SignalR.Redis` , llame a <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

* Configure las opciones según sea necesario:
 
  La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.

  En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto. En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Al utilizar `Microsoft.AspNetCore.SignalR.Redis` , llame a <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

  En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.

  Para obtener información sobre las opciones de Redis, consulte la [documentación de StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* En la SignalR aplicación, instale el siguiente paquete de NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* En el `Startup.ConfigureServices` método, llame a <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Configure las opciones según sea necesario:
 
  La mayoría de las opciones se pueden establecer en la cadena de conexión o en el objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Las opciones especificadas en `ConfigurationOptions` invalidan las definidas en la cadena de conexión.

  En el ejemplo siguiente se muestra cómo establecer opciones en el `ConfigurationOptions` objeto. En este ejemplo se agrega un prefijo de canal para que varias aplicaciones puedan compartir la misma instancia de Redis, tal y como se explica en el paso siguiente.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  En el código anterior, `options.Configuration` se inicializa con lo que se haya especificado en la cadena de conexión.

  Para obtener información sobre las opciones de Redis, consulte la [documentación de StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Si usa un servidor de Redis para varias SignalR aplicaciones, use un prefijo de canal diferente para cada SignalR aplicación.

  La configuración de un prefijo de canal aísla una SignalR aplicación de otras que usan prefijos de canal diferentes. Si no asigna prefijos diferentes, un mensaje enviado desde una aplicación a todos sus clientes irá a todos los clientes de todas las aplicaciones que usan el servidor de Redis como un backplane.

* Configure el software de equilibrio de carga de la granja de servidores para sesiones permanentes. Estos son algunos ejemplos de documentación sobre cómo hacerlo:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Errores del servidor de Redis

Cuando un servidor de Redis deja de funcionar, SignalR produce excepciones que indican que los mensajes no se entregarán. Algunos mensajes de excepción típicos:

* *Error al escribir el mensaje*
* *No se pudo invocar el método de concentrador ' MethodName '*
* *Error en la conexión a Redis*

SignalR no almacena en búfer los mensajes para enviarlos cuando el servidor vuelve a copia de seguridad. Los mensajes enviados mientras el servidor de Redis está inactivo se pierden.

SignalR se vuelve a conectar automáticamente cuando el servidor de Redis está disponible de nuevo.

### <a name="custom-behavior-for-connection-failures"></a>Comportamiento personalizado para errores de conexión

Este es un ejemplo que muestra cómo controlar los eventos de error de conexión de Redis.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Agrupación en clústeres de Redis

La [agrupación en clústeres de Redis](https://redis.io/topics/cluster-spec) es un método para lograr alta disponibilidad mediante el uso de varios servidores de Redis. La agrupación en clústeres no se admite oficialmente, pero podría funcionar.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea los siguientes recursos:

* <xref:signalr/scale>
* [Documentación de Redis](https://redis.io/documentation)
* [Documentación de Redis StackExchange](https://stackexchange.github.io/StackExchange.Redis/)
* [Documentación de Azure Redis Cache](/azure/redis-cache/)