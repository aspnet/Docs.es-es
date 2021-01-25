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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="d57d5-103">Configuración de opciones para el servidor web Kestrel de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d57d5-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="d57d5-104">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="d57d5-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d57d5-105">Para proporcionar configuración adicional después de llamar a `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="d57d5-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="d57d5-106">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="d57d5-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d57d5-107">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="d57d5-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d57d5-108">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="d57d5-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d57d5-109">En los ejemplos que se muestran más adelante en este artículo, las opciones de Kestrel se configuran en código de C#.</span><span class="sxs-lookup"><span data-stu-id="d57d5-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="d57d5-110">Las opciones de Kestrel también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d57d5-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d57d5-111">Por ejemplo, el [proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="d57d5-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="d57d5-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> y la [configuración del punto de conexión](xref:fundamentals/servers/kestrel/endpoints) se pueden establecer a partir de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="d57d5-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="d57d5-113">El resto de la configuración de Kestrel debe establecerse en código de C#.</span><span class="sxs-lookup"><span data-stu-id="d57d5-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="d57d5-114">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="d57d5-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d57d5-115">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d57d5-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d57d5-116">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d57d5-117">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d57d5-118">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d57d5-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="d57d5-119">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="d57d5-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d57d5-120">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d57d5-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d57d5-121">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d57d5-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="d57d5-122">Límites generales</span><span class="sxs-lookup"><span data-stu-id="d57d5-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d57d5-123">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="d57d5-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d57d5-124">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="d57d5-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d57d5-125">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="d57d5-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d57d5-126">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="d57d5-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d57d5-127">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="d57d5-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d57d5-128">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d57d5-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d57d5-129">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d57d5-130">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="d57d5-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d57d5-131">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="d57d5-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d57d5-132">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="d57d5-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d57d5-133">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="d57d5-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d57d5-134">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="d57d5-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d57d5-135">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="d57d5-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d57d5-136">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d57d5-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d57d5-137">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="d57d5-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d57d5-138">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), se deshabilita el límite de tamaño del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d57d5-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="d57d5-139">IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="d57d5-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d57d5-140">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="d57d5-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d57d5-141">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="d57d5-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d57d5-142">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel permite al cliente aumentar su velocidad de envío hasta el mínimo.</span><span class="sxs-lookup"><span data-stu-id="d57d5-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="d57d5-143">Durante ese tiempo no se comprueba la velocidad.</span><span class="sxs-lookup"><span data-stu-id="d57d5-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="d57d5-144">El período de gracia permite evitar que se interrumpan las conexiones que inicialmente envían datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="d57d5-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="d57d5-145">La velocidad mínima predeterminada es de 240 bytes por segundo, con un período de gracia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="d57d5-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="d57d5-146">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d57d5-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d57d5-147">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="d57d5-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d57d5-148">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d57d5-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="d57d5-149">Invalide los límites de velocidad mínima por solicitud en el middleware:</span><span class="sxs-lookup"><span data-stu-id="d57d5-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d57d5-150">El elemento <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> al que se hace referencia en el ejemplo anterior no está presente en `HttpContext.Features` para las solicitudes HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d57d5-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="d57d5-151">La modificación de los límites de velocidad por cada solicitud no suele ser compatible con HTTP/2, debido a la compatibilidad del protocolo con la multiplexación de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d57d5-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d57d5-152">Sin embargo, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> sigue estando presente en `HttpContext.Features` para las solicitudes HTTP/2, ya que el límite de velocidad de lectura aún puede estar *completamente deshabilitado* por solicitud estableciendo `IHttpMinRequestBodyDataRateFeature.MinDataRate` en `null` incluso para una solicitud HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d57d5-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="d57d5-153">Si se intenta leer `IHttpMinRequestBodyDataRateFeature.MinDataRate` o se intenta su establecimiento en un valor distinto de `null`, se obtendrá una excepción `NotSupportedException` con una solicitud HTTP/2 dada.</span><span class="sxs-lookup"><span data-stu-id="d57d5-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="d57d5-154">Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d57d5-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d57d5-155">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="d57d5-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d57d5-156">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d57d5-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d57d5-157">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="d57d5-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="d57d5-158">Límites de HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d57d5-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d57d5-159">Secuencias máximas por conexión</span><span class="sxs-lookup"><span data-stu-id="d57d5-159">Maximum streams per connection</span></span>

<span data-ttu-id="d57d5-160">`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d57d5-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d57d5-161">Se rechazarán las secuencias en exceso.</span><span class="sxs-lookup"><span data-stu-id="d57d5-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="d57d5-162">El valor predeterminado es 100.</span><span class="sxs-lookup"><span data-stu-id="d57d5-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d57d5-163">Tamaño de la tabla de encabezado</span><span class="sxs-lookup"><span data-stu-id="d57d5-163">Header table size</span></span>

<span data-ttu-id="d57d5-164">El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d57d5-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d57d5-165">`Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="d57d5-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d57d5-166">El valor se proporciona en octetos y debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="d57d5-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="d57d5-167">El valor predeterminado es 4096.</span><span class="sxs-lookup"><span data-stu-id="d57d5-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d57d5-168">Tamaño máximo de marco</span><span class="sxs-lookup"><span data-stu-id="d57d5-168">Maximum frame size</span></span>

<span data-ttu-id="d57d5-169">`Http2.MaxFrameSize` indica el tamaño máximo permitido de una carga de marco de conexión HTTP/2 recibida o enviada por el servidor.</span><span class="sxs-lookup"><span data-stu-id="d57d5-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="d57d5-170">El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="d57d5-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="d57d5-171">El valor predeterminado es 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="d57d5-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d57d5-172">Tamaño máximo del encabezado de solicitud</span><span class="sxs-lookup"><span data-stu-id="d57d5-172">Maximum request header size</span></span>

<span data-ttu-id="d57d5-173">`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="d57d5-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d57d5-174">Este límite se aplica al nombre y al valor en sus representaciones comprimidas y no comprimidas.</span><span class="sxs-lookup"><span data-stu-id="d57d5-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d57d5-175">El valor debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="d57d5-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="d57d5-176">El valor predeterminado es 8192.</span><span class="sxs-lookup"><span data-stu-id="d57d5-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d57d5-177">Tamaño inicial de la ventana de conexión</span><span class="sxs-lookup"><span data-stu-id="d57d5-177">Initial connection window size</span></span>

<span data-ttu-id="d57d5-178">`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión.</span><span class="sxs-lookup"><span data-stu-id="d57d5-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d57d5-179">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d57d5-180">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d57d5-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="d57d5-181">El valor predeterminado es 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="d57d5-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d57d5-182">Tamaño inicial de la ventana de transmisión</span><span class="sxs-lookup"><span data-stu-id="d57d5-182">Initial stream window size</span></span>

<span data-ttu-id="d57d5-183">`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión).</span><span class="sxs-lookup"><span data-stu-id="d57d5-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d57d5-184">Las solicitudes también están limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="d57d5-185">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d57d5-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="d57d5-186">El valor predeterminado es 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="d57d5-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="d57d5-187">Configuración de pings de mantenimiento de conexión HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d57d5-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="d57d5-188">Kestrel se puede configurar de modo que envíe pings HTTP/2 a los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="d57d5-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="d57d5-189">Los pings HTTP/2 tienen varios fines:</span><span class="sxs-lookup"><span data-stu-id="d57d5-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="d57d5-190">Mantener activas las conexiones inactivas.</span><span class="sxs-lookup"><span data-stu-id="d57d5-190">Keep idle connections alive.</span></span> <span data-ttu-id="d57d5-191">Algunos clientes y servidores proxy cierran las conexiones que están inactivas.</span><span class="sxs-lookup"><span data-stu-id="d57d5-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="d57d5-192">Los pings HTTP/2 se consideran una actividad en una conexión y evitan que la conexión se cierre por considerarse inactiva.</span><span class="sxs-lookup"><span data-stu-id="d57d5-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="d57d5-193">Cerrar las conexiones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="d57d5-193">Close unhealthy connections.</span></span> <span data-ttu-id="d57d5-194">El servidor cierra aquellas conexiones en las que el cliente no responde al ping de mantenimiento de conexión en el tiempo configurado.</span><span class="sxs-lookup"><span data-stu-id="d57d5-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="d57d5-195">Hay dos opciones de configuración relacionadas con los pings de mantenimiento de conexión HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="d57d5-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="d57d5-196">`Http2.KeepAlivePingInterval` es un elemento `TimeSpan` que configura el intervalo de ping.</span><span class="sxs-lookup"><span data-stu-id="d57d5-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="d57d5-197">El servidor envía un ping de mantenimiento de conexión al cliente si no recibe ningún fotograma durante este período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="d57d5-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="d57d5-198">Los pings de mantenimiento de conexión se deshabilitan cuando esta opción se establece en `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="d57d5-199">El valor predeterminado es `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="d57d5-200">`Http2.KeepAlivePingTimeout` es un `TimeSpan` que configura el tiempo de espera de los pings.</span><span class="sxs-lookup"><span data-stu-id="d57d5-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="d57d5-201">Si el servidor no recibe ningún fotograma, como un ping de respuesta, durante este tiempo de espera, se cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="d57d5-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="d57d5-202">El tiempo de espera de mantenimiento de conexión se deshabilita cuando esta opción se establece en `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="d57d5-203">El valor predeterminado es 20 segundos.</span><span class="sxs-lookup"><span data-stu-id="d57d5-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="d57d5-204">Otras opciones</span><span class="sxs-lookup"><span data-stu-id="d57d5-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d57d5-205">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="d57d5-205">Synchronous I/O</span></span>

<span data-ttu-id="d57d5-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d57d5-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d57d5-207">El valor predeterminado es `false`.</span><span class="sxs-lookup"><span data-stu-id="d57d5-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="d57d5-208">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="d57d5-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d57d5-209">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="d57d5-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d57d5-210">En el ejemplo siguiente se habilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="d57d5-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d57d5-211">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="d57d5-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
