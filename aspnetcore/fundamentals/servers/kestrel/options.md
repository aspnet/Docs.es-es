---
title: Configuración de opciones para el servidor web Kestrel de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la configuración de opciones para Kestrel, el servidor web multiplataforma de ASP.NET Core.
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253892"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>Configuración de opciones para el servidor web Kestrel de ASP.NET Core

El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.

Para proporcionar configuración adicional después de llamar a `ConfigureWebHostDefaults`, use `ConfigureKestrel`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

En los ejemplos que se muestran más adelante en este artículo, las opciones de Kestrel se configuran en código de C#. Las opciones de Kestrel también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index). Por ejemplo, el [proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> y la [configuración del punto de conexión](xref:fundamentals/servers/kestrel/endpoints) se pueden establecer a partir de proveedores de configuración. El resto de la configuración de Kestrel debe establecerse en código de C#.

Siga **uno** de estos procedimientos:

* Configure Kestrel en `Startup.ConfigureServices`:

  1. Inserte una instancia de `IConfiguration` en la clase `Startup`. En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.
  2. En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Configure Kestrel al compilar el host:

  En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).

## <a name="general-limits"></a>Límites generales

### <a name="keep-alive-timeout"></a>Tiempo de expiración de la conexión persistente

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5). El valor predeterminado es de 2 minutos.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Las conexiones máximas de cliente

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets). Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

El número máximo de conexiones es ilimitado de forma predeterminada (null).

### <a name="maximum-request-body-size"></a>El tamaño máximo del cuerpo de solicitud

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.

El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Invalide la configuración en una solicitud específica de middleware:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud. Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.

Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), se deshabilita el límite de tamaño del cuerpo de la solicitud de Kestrel. IIS ya establece el límite.

### <a name="minimum-request-body-data-rate"></a>La velocidad mínima de los datos del cuerpo de solicitud.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo. Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel permite al cliente aumentar su velocidad de envío hasta el mínimo. Durante ese tiempo no se comprueba la velocidad. El período de gracia permite evitar que se interrumpan las conexiones que inicialmente envían datos a una velocidad lenta debido a un inicio lento de TCP.

La velocidad mínima predeterminada es de 240 bytes por segundo, con un período de gracia de 5 segundos.

También se aplica una velocidad mínima a la respuesta. El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.

Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Invalide los límites de velocidad mínima por solicitud en el middleware:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

El elemento <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> al que se hace referencia en el ejemplo anterior no está presente en `HttpContext.Features` para las solicitudes HTTP/2. La modificación de los límites de velocidad por cada solicitud no suele ser compatible con HTTP/2, debido a la compatibilidad del protocolo con la multiplexación de solicitudes. Sin embargo, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> sigue estando presente en `HttpContext.Features` para las solicitudes HTTP/2, ya que el límite de velocidad de lectura aún puede estar *completamente deshabilitado* por solicitud estableciendo `IHttpMinRequestBodyDataRateFeature.MinDataRate` en `null` incluso para una solicitud HTTP/2. Si se intenta leer `IHttpMinRequestBodyDataRateFeature.MinDataRate` o se intenta su establecimiento en un valor distinto de `null`, se obtendrá una excepción `NotSupportedException` con una solicitud HTTP/2 dada.

Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.

### <a name="request-headers-timeout"></a>Tiempo de expiración de los encabezados de solicitud

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes. El valor predeterminado es 30 segundos.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>Límites de HTTP/2

### <a name="maximum-streams-per-connection"></a>Secuencias máximas por conexión

`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2. Se rechazarán las secuencias en exceso.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

El valor predeterminado es 100.

### <a name="header-table-size"></a>Tamaño de la tabla de encabezado

El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2. `Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK. El valor se proporciona en octetos y debe ser mayor que cero (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

El valor predeterminado es 4096.

### <a name="maximum-frame-size"></a>Tamaño máximo de marco

`Http2.MaxFrameSize` indica el tamaño máximo permitido de una carga de marco de conexión HTTP/2 recibida o enviada por el servidor. El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

El valor predeterminado es 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Tamaño máximo del encabezado de solicitud

`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud. Este límite se aplica al nombre y al valor en sus representaciones comprimidas y no comprimidas. El valor debe ser mayor que cero (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

El valor predeterminado es 8192.

### <a name="initial-connection-window-size"></a>Tamaño inicial de la ventana de conexión

`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión. Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`. El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

El valor predeterminado es 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Tamaño inicial de la ventana de transmisión

`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión). Las solicitudes también están limitadas por `Http2.InitialConnectionWindowSize`. El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

El valor predeterminado es 96 KB (98 304).

### <a name="http2-keep-alive-ping-configuration"></a>Configuración de pings de mantenimiento de conexión HTTP/2

Kestrel se puede configurar de modo que envíe pings HTTP/2 a los clientes conectados. Los pings HTTP/2 tienen varios fines:

* Mantener activas las conexiones inactivas. Algunos clientes y servidores proxy cierran las conexiones que están inactivas. Los pings HTTP/2 se consideran una actividad en una conexión y evitan que la conexión se cierre por considerarse inactiva.
* Cerrar las conexiones incorrectas. El servidor cierra aquellas conexiones en las que el cliente no responde al ping de mantenimiento de conexión en el tiempo configurado.

Hay dos opciones de configuración relacionadas con los pings de mantenimiento de conexión HTTP/2:

* `Http2.KeepAlivePingInterval` es un elemento `TimeSpan` que configura el intervalo de ping. El servidor envía un ping de mantenimiento de conexión al cliente si no recibe ningún fotograma durante este período de tiempo. Los pings de mantenimiento de conexión se deshabilitan cuando esta opción se establece en `TimeSpan.MaxValue`. El valor predeterminado es `TimeSpan.MaxValue`.
* `Http2.KeepAlivePingTimeout` es un `TimeSpan` que configura el tiempo de espera de los pings. Si el servidor no recibe ningún fotograma, como un ping de respuesta, durante este tiempo de espera, se cierra la conexión. El tiempo de espera de mantenimiento de conexión se deshabilita cuando esta opción se establece en `TimeSpan.MaxValue`. El valor predeterminado es 20 segundos.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Otras opciones

### <a name="synchronous-io"></a>E/S síncrona

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta. El valor predeterminado es `false`.

> [!WARNING]
> Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda. Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.

En el ejemplo siguiente se habilita la E/S sincrónica:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Para más información sobre otras opciones y límites de Kestrel, vea:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
