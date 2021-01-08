---
title: Implementación del servidor web Kestrel en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre Kestrel, el servidor web multiplataforma de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5c9e1717ad603687343f015826a113e6945e4a41
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854618"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="6709b-103">Implementación del servidor web Kestrel en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6709b-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="6709b-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) y [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="6709b-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6709b-105">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6709b-106">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6709b-107">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="6709b-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6709b-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6709b-108">HTTPS</span></span>
* <span data-ttu-id="6709b-109">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6709b-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6709b-110">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="6709b-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6709b-111">HTTP/2 (excepto en macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="6709b-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6709b-112">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="6709b-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6709b-113">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6709b-114">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6709b-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6709b-115">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6709b-115">HTTP/2 support</span></span>

<span data-ttu-id="6709b-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="6709b-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6709b-117">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="6709b-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="6709b-118">Windows Server 2016/Windows 10 o posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6709b-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6709b-119">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="6709b-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6709b-120">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="6709b-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6709b-121">Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="6709b-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6709b-122">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="6709b-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6709b-123">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="6709b-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6709b-124">&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6709b-125">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="6709b-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6709b-126">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6709b-127">Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="6709b-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6709b-128">HTTP/2 está deshabilitado de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6709b-129">Para obtener más información sobre la configuración, consulte las secciones [Opciones de Kestrel](#kestrel-options) y [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="6709b-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6709b-130">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="6709b-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6709b-131">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6709b-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6709b-132">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6709b-133">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="6709b-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6709b-135">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="6709b-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6709b-137">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="6709b-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6709b-138">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="6709b-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6709b-139">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6709b-140">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6709b-141">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="6709b-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6709b-142">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-142">A reverse proxy:</span></span>

* <span data-ttu-id="6709b-143">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="6709b-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6709b-144">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="6709b-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6709b-145">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="6709b-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6709b-146">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6709b-147">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="6709b-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-148">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6709b-149">Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6709b-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6709b-150">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6709b-151">En *Program.cs*, el método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="6709b-152">Para más información sobre la creación del host, vea las secciones *Configuración de un host* y *Configuración predeterminada del generador* de <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="6709b-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="6709b-153">Para proporcionar configuración adicional después de llamar a `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="6709b-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="6709b-154">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="6709b-154">Kestrel options</span></span>

<span data-ttu-id="6709b-155">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="6709b-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6709b-156">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6709b-157">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="6709b-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6709b-158">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="6709b-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6709b-159">En los ejemplos que se muestran más adelante en este artículo, las opciones de Kestrel se configuran en código de C#.</span><span class="sxs-lookup"><span data-stu-id="6709b-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="6709b-160">Las opciones de Kestrel también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6709b-161">Por ejemplo, el [proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="6709b-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> y la [configuración del punto de conexión](#endpoint-configuration) se pueden establecer a partir de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="6709b-163">El resto de la configuración de Kestrel debe establecerse en código de C#.</span><span class="sxs-lookup"><span data-stu-id="6709b-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="6709b-164">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="6709b-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6709b-165">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6709b-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6709b-166">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6709b-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6709b-167">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="6709b-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6709b-168">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6709b-169">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="6709b-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6709b-170">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6709b-171">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6709b-172">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="6709b-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6709b-173">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6709b-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6709b-174">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="6709b-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6709b-175">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="6709b-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6709b-176">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="6709b-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6709b-177">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6709b-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6709b-178">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="6709b-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6709b-179">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="6709b-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6709b-180">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6709b-181">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6709b-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6709b-182">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="6709b-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6709b-183">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="6709b-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6709b-184">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="6709b-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6709b-185">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6709b-186">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6709b-187">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6709b-188">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6709b-189">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="6709b-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6709b-190">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="6709b-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6709b-191">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="6709b-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6709b-192">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6709b-193">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6709b-194">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="6709b-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6709b-195">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6709b-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="6709b-196">Invalide los límites de velocidad mínima por solicitud en el middleware:</span><span class="sxs-lookup"><span data-stu-id="6709b-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6709b-197">La característica <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> a la que se hace referencia en el ejemplo anterior no está presente en `HttpContext.Features` para las solicitudes HTTP/2, porque generalmente no se permite modificar los límites de velocidad por solicitud debido a la compatibilidad del protocolo con la multiplexación de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6709b-198">Sin embargo, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> sigue estando presente en `HttpContext.Features` para las solicitudes HTTP/2, ya que el límite de velocidad de lectura aún puede estar *completamente deshabilitado* por solicitud estableciendo `IHttpMinRequestBodyDataRateFeature.MinDataRate` en `null` incluso para una solicitud HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="6709b-199">Si se intenta leer `IHttpMinRequestBodyDataRateFeature.MinDataRate` o se intenta su establecimiento en un valor distinto de `null`, se obtendrá una excepción `NotSupportedException` con una solicitud HTTP/2 dada.</span><span class="sxs-lookup"><span data-stu-id="6709b-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="6709b-200">Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6709b-201">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6709b-202">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6709b-203">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6709b-204">Secuencias máximas por conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-204">Maximum streams per connection</span></span>

<span data-ttu-id="6709b-205">`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6709b-206">Se rechazarán las secuencias en exceso.</span><span class="sxs-lookup"><span data-stu-id="6709b-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="6709b-207">El valor predeterminado es 100.</span><span class="sxs-lookup"><span data-stu-id="6709b-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6709b-208">Tamaño de la tabla de encabezado</span><span class="sxs-lookup"><span data-stu-id="6709b-208">Header table size</span></span>

<span data-ttu-id="6709b-209">El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6709b-210">`Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="6709b-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6709b-211">El valor se proporciona en octetos y debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="6709b-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="6709b-212">El valor predeterminado es 4096.</span><span class="sxs-lookup"><span data-stu-id="6709b-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6709b-213">Tamaño máximo de marco</span><span class="sxs-lookup"><span data-stu-id="6709b-213">Maximum frame size</span></span>

<span data-ttu-id="6709b-214">`Http2.MaxFrameSize` indica el tamaño máximo permitido de una carga de marco de conexión HTTP/2 recibida o enviada por el servidor.</span><span class="sxs-lookup"><span data-stu-id="6709b-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="6709b-215">El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="6709b-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="6709b-216">El valor predeterminado es 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="6709b-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6709b-217">Tamaño máximo del encabezado de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-217">Maximum request header size</span></span>

<span data-ttu-id="6709b-218">`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6709b-219">Este límite se aplica al nombre y al valor en sus representaciones comprimidas y no comprimidas.</span><span class="sxs-lookup"><span data-stu-id="6709b-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6709b-220">El valor debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="6709b-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="6709b-221">El valor predeterminado es 8192.</span><span class="sxs-lookup"><span data-stu-id="6709b-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6709b-222">Tamaño inicial de la ventana de conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-222">Initial connection window size</span></span>

<span data-ttu-id="6709b-223">`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6709b-224">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="6709b-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6709b-225">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6709b-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="6709b-226">El valor predeterminado es 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="6709b-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6709b-227">Tamaño inicial de la ventana de transmisión</span><span class="sxs-lookup"><span data-stu-id="6709b-227">Initial stream window size</span></span>

<span data-ttu-id="6709b-228">`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión).</span><span class="sxs-lookup"><span data-stu-id="6709b-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6709b-229">Las solicitudes también están limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="6709b-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="6709b-230">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6709b-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="6709b-231">El valor predeterminado es 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="6709b-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="6709b-232">Configuración de pings de mantenimiento de conexión HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6709b-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="6709b-233">Kestrel se puede configurar de modo que envíe pings HTTP/2 a los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="6709b-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="6709b-234">Los pings HTTP/2 tienen varios fines:</span><span class="sxs-lookup"><span data-stu-id="6709b-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="6709b-235">Mantener activas las conexiones inactivas.</span><span class="sxs-lookup"><span data-stu-id="6709b-235">Keep idle connections alive.</span></span> <span data-ttu-id="6709b-236">Algunos clientes y servidores proxy cierran las conexiones que están inactivas.</span><span class="sxs-lookup"><span data-stu-id="6709b-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="6709b-237">Los pings HTTP/2 se consideran una actividad en una conexión y evitan que la conexión se cierre por considerarse inactiva.</span><span class="sxs-lookup"><span data-stu-id="6709b-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="6709b-238">Cerrar las conexiones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="6709b-238">Close unhealthy connections.</span></span> <span data-ttu-id="6709b-239">El servidor cierra aquellas conexiones en las que el cliente no responde al ping de mantenimiento de conexión en el tiempo configurado.</span><span class="sxs-lookup"><span data-stu-id="6709b-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="6709b-240">Hay dos opciones de configuración relacionadas con los pings de mantenimiento de conexión HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6709b-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="6709b-241">`Http2.KeepAlivePingInterval` es un `TimeSpan` que configura el intervalo de pings.</span><span class="sxs-lookup"><span data-stu-id="6709b-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="6709b-242">El servidor envía un ping de mantenimiento de conexión al cliente si no recibe ningún fotograma durante este período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="6709b-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="6709b-243">Los pings de mantenimiento de conexión se deshabilitan cuando esta opción se establece en `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="6709b-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="6709b-244">El valor predeterminado es `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="6709b-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="6709b-245">`Http2.KeepAlivePingTimeout` es un `TimeSpan` que configura el tiempo de espera de los pings.</span><span class="sxs-lookup"><span data-stu-id="6709b-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="6709b-246">Si el servidor no recibe ningún fotograma, como un ping de respuesta, durante este tiempo de espera, se cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="6709b-247">El tiempo de espera de mantenimiento de conexión se deshabilita cuando esta opción se establece en `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="6709b-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="6709b-248">El valor predeterminado es 20 segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="6709b-249">Clips finales</span><span class="sxs-lookup"><span data-stu-id="6709b-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="6709b-250">Reset</span><span class="sxs-lookup"><span data-stu-id="6709b-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="6709b-251">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="6709b-251">Synchronous I/O</span></span>

<span data-ttu-id="6709b-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6709b-253">El valor predeterminado es `false`.</span><span class="sxs-lookup"><span data-stu-id="6709b-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-254">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="6709b-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6709b-255">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="6709b-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6709b-256">En el ejemplo siguiente se habilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="6709b-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6709b-257">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="6709b-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6709b-258">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-258">Endpoint configuration</span></span>

<span data-ttu-id="6709b-259">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="6709b-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6709b-260">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="6709b-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6709b-261">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="6709b-261">Specify URLs using the:</span></span>

* <span data-ttu-id="6709b-262">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="6709b-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6709b-263">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6709b-264">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="6709b-265">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="6709b-266">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6709b-267">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6709b-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6709b-268">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6709b-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6709b-269">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="6709b-269">A development certificate is created:</span></span>

* <span data-ttu-id="6709b-270">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="6709b-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6709b-271">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="6709b-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6709b-272">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="6709b-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6709b-273">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6709b-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6709b-274">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6709b-275">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6709b-276">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="6709b-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6709b-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6709b-278">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6709b-279">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="6709b-280">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6709b-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6709b-282">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6709b-283">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="6709b-284">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="6709b-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6709b-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="6709b-286">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="6709b-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6709b-287">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6709b-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6709b-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6709b-289">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6709b-290">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6709b-291">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6709b-292">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="6709b-292">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="6709b-293">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6709b-294">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6709b-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6709b-295">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6709b-296">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6709b-297">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6709b-298">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6709b-299">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6709b-300">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="6709b-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6709b-301">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6709b-302">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6709b-303">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6709b-304">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6709b-305">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="6709b-305">Supported configurations described next:</span></span>

* <span data-ttu-id="6709b-306">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-306">No configuration</span></span>
* <span data-ttu-id="6709b-307">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6709b-308">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="6709b-308">Change the defaults in code</span></span>

<span data-ttu-id="6709b-309">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-309">*No configuration*</span></span>

<span data-ttu-id="6709b-310">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6709b-311">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6709b-312">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6709b-313">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6709b-314">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6709b-315">En el ejemplo *appsettings.json* siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6709b-316">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="6709b-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6709b-317">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="6709b-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="6709b-318">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6709b-319">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="6709b-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6709b-320">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="6709b-320">Schema notes:</span></span>

* <span data-ttu-id="6709b-321">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6709b-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6709b-322">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="6709b-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6709b-323">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6709b-324">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="6709b-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6709b-325">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="6709b-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6709b-326">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6709b-327">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="6709b-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="6709b-328">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6709b-329">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="6709b-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6709b-330">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="6709b-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6709b-331">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6709b-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="6709b-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="6709b-333">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="6709b-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6709b-334">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="6709b-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6709b-335">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="6709b-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6709b-336">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6709b-337">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="6709b-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6709b-338">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="6709b-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6709b-339">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6709b-340">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="6709b-340">*Change the defaults in code*</span></span>

<span data-ttu-id="6709b-341">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6709b-342">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="6709b-343">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="6709b-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6709b-344">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6709b-345">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="6709b-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6709b-346">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6709b-347">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="6709b-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6709b-348">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="6709b-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6709b-349">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-349">SNI support requires:</span></span>

* <span data-ttu-id="6709b-350">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6709b-351">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="6709b-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6709b-352">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6709b-353">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6709b-354">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="6709b-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="6709b-355">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="6709b-355">Connection logging</span></span>

<span data-ttu-id="6709b-356">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6709b-357">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="6709b-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6709b-358">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6709b-359">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6709b-360">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="6709b-360">Bind to a TCP socket</span></span>

<span data-ttu-id="6709b-361">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="6709b-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="6709b-362">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6709b-363">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="6709b-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6709b-364">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="6709b-364">Bind to a Unix socket</span></span>

<span data-ttu-id="6709b-365">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6709b-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="6709b-366">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="6709b-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6709b-367">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="6709b-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6709b-368">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="6709b-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="6709b-369">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="6709b-369">Port 0</span></span>

<span data-ttu-id="6709b-370">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="6709b-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6709b-371">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="6709b-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6709b-372">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6709b-373">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="6709b-373">Limitations</span></span>

<span data-ttu-id="6709b-374">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="6709b-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6709b-375">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="6709b-376">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-376">`urls` host configuration key</span></span>
* <span data-ttu-id="6709b-377">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="6709b-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6709b-378">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6709b-379">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="6709b-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6709b-380">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="6709b-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6709b-381">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6709b-382">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="6709b-382">IIS endpoint configuration</span></span>

<span data-ttu-id="6709b-383">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6709b-384">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="6709b-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6709b-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="6709b-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="6709b-386">La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor.</span><span class="sxs-lookup"><span data-stu-id="6709b-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6709b-387">Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="6709b-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6709b-388">Valor de enumeración `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="6709b-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6709b-389">Protocolo de conexión permitido</span><span class="sxs-lookup"><span data-stu-id="6709b-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6709b-390">HTTP/1.1 solo.</span><span class="sxs-lookup"><span data-stu-id="6709b-390">HTTP/1.1 only.</span></span> <span data-ttu-id="6709b-391">Puede usarse con o sin TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6709b-392">HTTP/2 solo.</span><span class="sxs-lookup"><span data-stu-id="6709b-392">HTTP/2 only.</span></span> <span data-ttu-id="6709b-393">Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="6709b-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6709b-394">HTTP/1.1 y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6709b-395">HTTP/2 necesita que el cliente seleccione HTTP/2 en el protocolo de enlace [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS; en caso contrario, el valor predeterminado de la conexión es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6709b-396">El valor `ListenOptions.Protocols` predeterminado de cualquier punto de conexión es `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="6709b-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="6709b-397">Restricciones de TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6709b-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6709b-398">TLS 1.2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="6709b-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6709b-399">Renegociación deshabilitada</span><span class="sxs-lookup"><span data-stu-id="6709b-399">Renegotiation disabled</span></span>
* <span data-ttu-id="6709b-400">Compresión deshabilitada</span><span class="sxs-lookup"><span data-stu-id="6709b-400">Compression disabled</span></span>
* <span data-ttu-id="6709b-401">Tamaños de intercambio de claves efímeras mínimos:</span><span class="sxs-lookup"><span data-stu-id="6709b-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6709b-402">Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="6709b-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="6709b-403">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="6709b-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="6709b-404">Conjunto de cifrado no prohibido.</span><span class="sxs-lookup"><span data-stu-id="6709b-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="6709b-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6709b-406">El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000.</span><span class="sxs-lookup"><span data-stu-id="6709b-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6709b-407">Las conexiones se protegen mediante TLS con un certificado proporcionado:</span><span class="sxs-lookup"><span data-stu-id="6709b-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="6709b-408">Si es necesario, use el middleware de conexión para filtrar los protocolos de enlace TLS por conexión en el caso de cifrados específicos:</span><span class="sxs-lookup"><span data-stu-id="6709b-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="6709b-409">En el ejemplo siguiente se produce una excepción <xref:System.NotSupportedException> con cualquier algoritmo de cifrado que no admita la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6709b-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="6709b-410">Como alternativa, defina y compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con una lista de conjuntos de cifrado aceptables.</span><span class="sxs-lookup"><span data-stu-id="6709b-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="6709b-411">No se usa ningún cifrado con un algoritmo de cifrado [CipherAlgorithmType.Null ](xref:System.Security.Authentication.CipherAlgorithmType).</span><span class="sxs-lookup"><span data-stu-id="6709b-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="6709b-412">El filtrado de conexiones también puede configurarse mediante una función lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="6709b-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="6709b-413">En Linux, se puede usar <xref:System.Net.Security.CipherSuitesPolicy> para filtrar los protocolos de enlace TLS por conexión:</span><span class="sxs-lookup"><span data-stu-id="6709b-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="6709b-414">*Defina el protocolo a partir de la configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6709b-415">`CreateDefaultBuilder` llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6709b-416">En el siguiente ejemplo de *appsettings.json* , se establece HTTP/1.1 como el protocolo de conexión predeterminado para todos los puntos de conexión:</span><span class="sxs-lookup"><span data-stu-id="6709b-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="6709b-417">En el siguiente ejemplo de *appsettings.json* se establece el protocolo de conexión HTTP/1.1 para un punto de conexión específico:</span><span class="sxs-lookup"><span data-stu-id="6709b-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="6709b-418">Los protocolos especificados en el código invalidan los valores establecidos por la configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-418">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="6709b-419">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="6709b-419">URL prefixes</span></span>

<span data-ttu-id="6709b-420">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="6709b-420">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6709b-421">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="6709b-421">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6709b-422">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-422">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6709b-423">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-423">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6709b-424">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-424">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6709b-425">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-425">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6709b-426">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-426">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6709b-427">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-427">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6709b-428">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="6709b-428">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6709b-429">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-429">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6709b-430">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="6709b-430">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6709b-431">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-431">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6709b-432">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-432">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6709b-433">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="6709b-433">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6709b-434">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="6709b-434">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6709b-435">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="6709b-435">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6709b-436">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="6709b-436">Host filtering</span></span>

<span data-ttu-id="6709b-437">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="6709b-437">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6709b-438">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="6709b-438">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6709b-439">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="6709b-439">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6709b-440">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="6709b-440">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6709b-441">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="6709b-441">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6709b-442">El middleware de filtrado de host lo proporciona el paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se proporciona implícitamente para aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-442">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="6709b-443">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-443">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6709b-444">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-444">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6709b-445">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="6709b-445">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6709b-446">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="6709b-446">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6709b-447">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-447">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6709b-448">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="6709b-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6709b-449">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="6709b-449">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6709b-450">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="6709b-450">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6709b-451">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-451">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6709b-452">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6709b-452">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="6709b-453">Configuración de transporte de Libuv</span><span class="sxs-lookup"><span data-stu-id="6709b-453">Libuv transport configuration</span></span>

<span data-ttu-id="6709b-454">A partir de ASP.NET Core 5.0, el transporte de Libuv de Kestrel está obsoleto.</span><span class="sxs-lookup"><span data-stu-id="6709b-454">As of ASP.NET Core 5.0, Kestrel's Libuv transport is obsolete.</span></span> <span data-ttu-id="6709b-455">El transporte de Libuv no recibe actualizaciones para admitir nuevas plataformas de sistema operativo, como Windows ARM64, y se eliminará en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="6709b-455">Libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="6709b-456">Quite todas las llamadas al método obsoleto <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> y, en su lugar, use el transporte de sockets predeterminado de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-456">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="6709b-457">Configuración de transporte de Libuv</span><span class="sxs-lookup"><span data-stu-id="6709b-457">Libuv transport configuration</span></span>

<span data-ttu-id="6709b-458">Para los proyectos que necesitan el uso de Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span><span class="sxs-lookup"><span data-stu-id="6709b-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="6709b-459">Agregue una dependencia para el paquete [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) al archivo del proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6709b-460">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="6709b-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6709b-461">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-461">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6709b-462">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-462">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6709b-463">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="6709b-463">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6709b-464">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6709b-464">HTTPS</span></span>
* <span data-ttu-id="6709b-465">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6709b-465">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6709b-466">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="6709b-466">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6709b-467">HTTP/2 (excepto en macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="6709b-467">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6709b-468">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="6709b-468">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6709b-469">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-469">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6709b-470">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6709b-470">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6709b-471">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6709b-471">HTTP/2 support</span></span>

<span data-ttu-id="6709b-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) está disponible para las aplicaciones de ASP.NET Core si se cumplen los siguientes requisitos básicos:</span><span class="sxs-lookup"><span data-stu-id="6709b-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6709b-473">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="6709b-473">Operating system&dagger;</span></span>
  * <span data-ttu-id="6709b-474">Windows Server 2016/Windows 10 o posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6709b-474">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6709b-475">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="6709b-475">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6709b-476">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="6709b-476">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6709b-477">Conexión con [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="6709b-477">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6709b-478">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="6709b-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6709b-479">&dagger;HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="6709b-479">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6709b-480">&Dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-480">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6709b-481">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="6709b-481">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6709b-482">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-482">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6709b-483">Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="6709b-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6709b-484">HTTP/2 está deshabilitado de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-484">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6709b-485">Para obtener más información sobre la configuración, consulte las secciones [Opciones de Kestrel](#kestrel-options) y [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="6709b-485">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6709b-486">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="6709b-486">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6709b-487">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6709b-487">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6709b-488">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-488">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6709b-489">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="6709b-489">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6709b-491">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="6709b-491">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6709b-493">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="6709b-493">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6709b-494">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="6709b-494">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6709b-495">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-495">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6709b-496">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-496">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6709b-497">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="6709b-497">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6709b-498">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-498">A reverse proxy:</span></span>

* <span data-ttu-id="6709b-499">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="6709b-499">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6709b-500">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="6709b-500">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6709b-501">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="6709b-501">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6709b-502">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-502">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6709b-503">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="6709b-503">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-504">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-504">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6709b-505">Cómo usar Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6709b-505">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6709b-506">El paquete [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6709b-506">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6709b-507">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-507">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6709b-508">En *Program.cs*, el código de plantilla llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que a su vez llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="6709b-508">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="6709b-509">Para más información sobre `CreateDefaultBuilder` y la compilación del host, consulte la sección sobre *cómo configurar un host* de <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="6709b-509">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="6709b-510">Para proporcionar configuración adicional después de llamar a `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="6709b-510">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6709b-511">Si la aplicación no llama a `CreateDefaultBuilder` para configurar el host, llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>**antes** de llamar a `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="6709b-511">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="6709b-512">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="6709b-512">Kestrel options</span></span>

<span data-ttu-id="6709b-513">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="6709b-513">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6709b-514">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-514">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6709b-515">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="6709b-515">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6709b-516">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="6709b-516">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6709b-517">Las opciones de Kestrel, que se configuran en código de C# en los siguientes ejemplos, también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-517">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6709b-518">Por ejemplo, el proveedor de configuración de archivo puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-518">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="6709b-519">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="6709b-519">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6709b-520">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6709b-520">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6709b-521">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6709b-521">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6709b-522">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="6709b-522">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6709b-523">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-523">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6709b-524">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="6709b-524">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6709b-525">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-525">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="6709b-526">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-526">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6709b-527">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="6709b-527">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6709b-528">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6709b-528">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6709b-529">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="6709b-529">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6709b-530">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="6709b-530">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6709b-531">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="6709b-531">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6709b-532">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6709b-532">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6709b-533">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="6709b-533">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6709b-534">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="6709b-534">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6709b-535">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-535">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6709b-536">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6709b-536">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6709b-537">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="6709b-537">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6709b-538">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="6709b-538">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6709b-539">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="6709b-539">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6709b-540">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-540">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6709b-541">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-541">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6709b-542">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-542">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6709b-543">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-543">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6709b-544">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="6709b-544">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6709b-545">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="6709b-545">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6709b-546">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="6709b-546">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6709b-547">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-547">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6709b-548">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-548">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6709b-549">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="6709b-549">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6709b-550">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6709b-550">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="6709b-551">Invalide los límites de velocidad mínima por solicitud en el middleware:</span><span class="sxs-lookup"><span data-stu-id="6709b-551">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6709b-552">Ninguna de las características de velocidad a las que se hace referencia en el ejemplo anterior están presentes en `HttpContext.Features` para las solicitudes HTTP/2, porque no se permite modificar los límites de velocidad por solicitud en HTTP/2 debido a la compatibilidad del protocolo con la multiplexación de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-552">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6709b-553">Se siguen aplicando límites de velocidad en todo el servidor configurados con `KestrelServerOptions.Limits` a las conexiones HTTP/1.x y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-553">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6709b-554">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-554">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6709b-555">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-555">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6709b-556">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-556">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6709b-557">Secuencias máximas por conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-557">Maximum streams per connection</span></span>

<span data-ttu-id="6709b-558">`Http2.MaxStreamsPerConnection` limita el número de secuencias de solicitudes simultáneas por conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-558">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6709b-559">Se rechazarán las secuencias en exceso.</span><span class="sxs-lookup"><span data-stu-id="6709b-559">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="6709b-560">El valor predeterminado es 100.</span><span class="sxs-lookup"><span data-stu-id="6709b-560">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6709b-561">Tamaño de la tabla de encabezado</span><span class="sxs-lookup"><span data-stu-id="6709b-561">Header table size</span></span>

<span data-ttu-id="6709b-562">El descodificador HPACK descomprime los encabezados HTTP para las conexiones HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-562">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6709b-563">`Http2.HeaderTableSize` limita el tamaño de la tabla de compresión de encabezado que usa el descodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="6709b-563">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6709b-564">El valor se proporciona en octetos y debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="6709b-564">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="6709b-565">El valor predeterminado es 4096.</span><span class="sxs-lookup"><span data-stu-id="6709b-565">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6709b-566">Tamaño máximo de marco</span><span class="sxs-lookup"><span data-stu-id="6709b-566">Maximum frame size</span></span>

<span data-ttu-id="6709b-567">`Http2.MaxFrameSize` indica el tamaño máximo de la carga útil de la trama de conexión HTTP/2 que se puede recibir.</span><span class="sxs-lookup"><span data-stu-id="6709b-567">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="6709b-568">El valor se proporciona en octetos y debe estar comprendido entre 2^14 (16 384) y 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="6709b-568">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="6709b-569">El valor predeterminado es 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="6709b-569">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6709b-570">Tamaño máximo del encabezado de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-570">Maximum request header size</span></span>

<span data-ttu-id="6709b-571">`Http2.MaxRequestHeaderFieldSize` indica el tamaño máximo permitido (en octetos) de los valores de los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-571">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6709b-572">Este límite se aplica al nombre y al valor conjuntamente en sus representaciones comprimidas y no comprimidas.</span><span class="sxs-lookup"><span data-stu-id="6709b-572">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6709b-573">El valor debe ser mayor que cero (0).</span><span class="sxs-lookup"><span data-stu-id="6709b-573">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="6709b-574">El valor predeterminado es 8192.</span><span class="sxs-lookup"><span data-stu-id="6709b-574">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6709b-575">Tamaño inicial de la ventana de conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-575">Initial connection window size</span></span>

<span data-ttu-id="6709b-576">`Http2.InitialConnectionWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez de forma agregada en todas las solicitudes (transmisiones) por conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-576">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6709b-577">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="6709b-577">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6709b-578">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6709b-578">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="6709b-579">El valor predeterminado es 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="6709b-579">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6709b-580">Tamaño inicial de la ventana de transmisión</span><span class="sxs-lookup"><span data-stu-id="6709b-580">Initial stream window size</span></span>

<span data-ttu-id="6709b-581">`Http2.InitialStreamWindowSize` indica la cantidad máxima de datos del cuerpo de solicitud (en bytes) que el servidor almacena en búfer a la vez por solicitud (transmisión).</span><span class="sxs-lookup"><span data-stu-id="6709b-581">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6709b-582">Las solicitudes también están limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="6709b-582">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6709b-583">El valor debe ser igual o mayor que 65 535 y menor que 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6709b-583">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="6709b-584">El valor predeterminado es 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="6709b-584">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6709b-585">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="6709b-585">Synchronous I/O</span></span>

<span data-ttu-id="6709b-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6709b-587">El valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="6709b-587">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-588">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="6709b-588">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6709b-589">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="6709b-589">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6709b-590">En el ejemplo siguiente se habilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="6709b-590">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6709b-591">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="6709b-591">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6709b-592">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-592">Endpoint configuration</span></span>

<span data-ttu-id="6709b-593">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="6709b-593">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6709b-594">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="6709b-594">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6709b-595">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="6709b-595">Specify URLs using the:</span></span>

* <span data-ttu-id="6709b-596">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="6709b-596">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6709b-597">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-597">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6709b-598">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-598">`urls` host configuration key.</span></span>
* <span data-ttu-id="6709b-599">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-599">`UseUrls` extension method.</span></span>

<span data-ttu-id="6709b-600">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-600">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6709b-601">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6709b-601">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6709b-602">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6709b-602">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6709b-603">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="6709b-603">A development certificate is created:</span></span>

* <span data-ttu-id="6709b-604">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="6709b-604">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6709b-605">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="6709b-605">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6709b-606">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="6709b-606">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6709b-607">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6709b-607">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6709b-608">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-608">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6709b-609">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-609">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6709b-610">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="6709b-610">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6709b-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6709b-612">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-612">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6709b-613">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-613">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="6709b-614">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6709b-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6709b-616">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-616">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6709b-617">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-617">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="6709b-618">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-618">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="6709b-619">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6709b-619">Configure(IConfiguration)</span></span>

<span data-ttu-id="6709b-620">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="6709b-620">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6709b-621">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-621">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6709b-622">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6709b-622">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6709b-623">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-623">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6709b-624">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-624">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6709b-625">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-625">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6709b-626">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="6709b-626">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="6709b-627">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-627">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6709b-628">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6709b-628">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6709b-629">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-629">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6709b-630">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-630">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6709b-631">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-631">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6709b-632">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-632">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6709b-633">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-633">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6709b-634">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="6709b-634">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6709b-635">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-635">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6709b-636">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-636">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6709b-637">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-637">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6709b-638">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-638">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6709b-639">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="6709b-639">Supported configurations described next:</span></span>

* <span data-ttu-id="6709b-640">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-640">No configuration</span></span>
* <span data-ttu-id="6709b-641">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-641">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6709b-642">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="6709b-642">Change the defaults in code</span></span>

<span data-ttu-id="6709b-643">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-643">*No configuration*</span></span>

<span data-ttu-id="6709b-644">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-644">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6709b-645">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-645">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6709b-646">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-646">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6709b-647">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-647">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6709b-648">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-648">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6709b-649">En el ejemplo *appsettings.json* siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-649">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6709b-650">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="6709b-650">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6709b-651">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="6709b-651">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="6709b-652">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-652">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6709b-653">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="6709b-653">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6709b-654">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="6709b-654">Schema notes:</span></span>

* <span data-ttu-id="6709b-655">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6709b-655">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6709b-656">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="6709b-656">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6709b-657">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-657">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6709b-658">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="6709b-658">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6709b-659">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="6709b-659">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6709b-660">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-660">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6709b-661">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="6709b-661">The `Certificate` section is optional.</span></span> <span data-ttu-id="6709b-662">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-662">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6709b-663">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="6709b-663">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6709b-664">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="6709b-664">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6709b-665">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-665">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6709b-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="6709b-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="6709b-667">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="6709b-667">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6709b-668">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="6709b-668">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6709b-669">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="6709b-669">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6709b-670">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-670">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6709b-671">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="6709b-671">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6709b-672">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="6709b-672">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6709b-673">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-673">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6709b-674">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="6709b-674">*Change the defaults in code*</span></span>

<span data-ttu-id="6709b-675">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-675">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6709b-676">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-676">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="6709b-677">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="6709b-677">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6709b-678">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6709b-679">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="6709b-679">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6709b-680">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-680">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6709b-681">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="6709b-681">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6709b-682">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="6709b-682">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6709b-683">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-683">SNI support requires:</span></span>

* <span data-ttu-id="6709b-684">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-684">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6709b-685">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="6709b-685">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6709b-686">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-686">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6709b-687">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-687">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6709b-688">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="6709b-688">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="6709b-689">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="6709b-689">Connection logging</span></span>

<span data-ttu-id="6709b-690">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-690">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6709b-691">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="6709b-691">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6709b-692">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-692">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6709b-693">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-693">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6709b-694">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="6709b-694">Bind to a TCP socket</span></span>

<span data-ttu-id="6709b-695">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="6709b-695">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="6709b-696">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-696">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6709b-697">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="6709b-697">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6709b-698">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="6709b-698">Bind to a Unix socket</span></span>

<span data-ttu-id="6709b-699">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6709b-699">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="6709b-700">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="6709b-700">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6709b-701">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="6709b-701">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6709b-702">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="6709b-702">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="6709b-703">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="6709b-703">Port 0</span></span>

<span data-ttu-id="6709b-704">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="6709b-704">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6709b-705">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="6709b-705">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6709b-706">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-706">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6709b-707">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="6709b-707">Limitations</span></span>

<span data-ttu-id="6709b-708">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="6709b-708">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6709b-709">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-709">`--urls` command-line argument</span></span>
* <span data-ttu-id="6709b-710">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-710">`urls` host configuration key</span></span>
* <span data-ttu-id="6709b-711">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="6709b-711">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6709b-712">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-712">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6709b-713">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="6709b-713">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6709b-714">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="6709b-714">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6709b-715">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-715">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6709b-716">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="6709b-716">IIS endpoint configuration</span></span>

<span data-ttu-id="6709b-717">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-717">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6709b-718">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="6709b-718">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6709b-719">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="6709b-719">ListenOptions.Protocols</span></span>

<span data-ttu-id="6709b-720">La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor.</span><span class="sxs-lookup"><span data-stu-id="6709b-720">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6709b-721">Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="6709b-721">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6709b-722">Valor de enumeración `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="6709b-722">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6709b-723">Protocolo de conexión permitido</span><span class="sxs-lookup"><span data-stu-id="6709b-723">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6709b-724">HTTP/1.1 solo.</span><span class="sxs-lookup"><span data-stu-id="6709b-724">HTTP/1.1 only.</span></span> <span data-ttu-id="6709b-725">Puede usarse con o sin TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-725">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6709b-726">HTTP/2 solo.</span><span class="sxs-lookup"><span data-stu-id="6709b-726">HTTP/2 only.</span></span> <span data-ttu-id="6709b-727">Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="6709b-727">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6709b-728">HTTP/1.1 y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-728">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6709b-729">HTTP/2 necesita TLS y una conexión de [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); en caso contrario, la opción predeterminada para la conexión es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-729">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6709b-730">El protocolo predeterminado es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-730">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="6709b-731">Restricciones de TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6709b-731">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6709b-732">TLS 1.2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="6709b-732">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6709b-733">Renegociación deshabilitada</span><span class="sxs-lookup"><span data-stu-id="6709b-733">Renegotiation disabled</span></span>
* <span data-ttu-id="6709b-734">Compresión deshabilitada</span><span class="sxs-lookup"><span data-stu-id="6709b-734">Compression disabled</span></span>
* <span data-ttu-id="6709b-735">Tamaños de intercambio de claves efímeras mínimos:</span><span class="sxs-lookup"><span data-stu-id="6709b-735">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6709b-736">Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="6709b-736">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="6709b-737">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="6709b-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="6709b-738">Conjunto de cifrado no bloqueado</span><span class="sxs-lookup"><span data-stu-id="6709b-738">Cipher suite not blocked</span></span>

<span data-ttu-id="6709b-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6709b-740">El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000.</span><span class="sxs-lookup"><span data-stu-id="6709b-740">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6709b-741">Las conexiones se protegen mediante TLS con un certificado proporcionado:</span><span class="sxs-lookup"><span data-stu-id="6709b-741">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="6709b-742">Opcionalmente, cree una implementación `IConnectionAdapter` para filtrar los protocolos de enlace TLS en una base por conexión para los cifrados específicos:</span><span class="sxs-lookup"><span data-stu-id="6709b-742">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="6709b-743">*Defina el protocolo a partir de la configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-743">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6709b-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6709b-745">En el siguiente ejemplo de *appsettings.json* se establece un protocolo de conexión predeterminado (HTTP/1.1 y HTTP/2) para todos los puntos de conexión de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-745">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="6709b-746">El siguiente ejemplo de archivo de configuración establece un protocolo de conexión para un punto de conexión específico:</span><span class="sxs-lookup"><span data-stu-id="6709b-746">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="6709b-747">Los protocolos especificados en el código invalidan los valores establecidos por la configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-747">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="6709b-748">Configuración de transporte de Libuv</span><span class="sxs-lookup"><span data-stu-id="6709b-748">Libuv transport configuration</span></span>

<span data-ttu-id="6709b-749">Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados.</span><span class="sxs-lookup"><span data-stu-id="6709b-749">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6709b-750">Se trata de un cambio muy importante para las aplicaciones ASP.NET Core 2.0 que actualizan a 2.1 y llaman a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>, y que dependen de cualquiera de los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="6709b-750">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6709b-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referencia de paquete directa)</span><span class="sxs-lookup"><span data-stu-id="6709b-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6709b-752">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6709b-752">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6709b-753">Para los proyectos que necesitan el uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="6709b-753">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6709b-754">Agregar una dependencia del paquete [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-754">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6709b-755">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-755">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="6709b-756">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="6709b-756">URL prefixes</span></span>

<span data-ttu-id="6709b-757">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="6709b-757">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6709b-758">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="6709b-758">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6709b-759">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-759">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6709b-760">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-760">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6709b-761">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-761">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6709b-762">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-762">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6709b-763">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-763">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6709b-764">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-764">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6709b-765">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="6709b-765">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6709b-766">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-766">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6709b-767">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="6709b-767">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6709b-768">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-768">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6709b-769">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-769">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6709b-770">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="6709b-770">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6709b-771">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="6709b-771">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6709b-772">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="6709b-772">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6709b-773">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="6709b-773">Host filtering</span></span>

<span data-ttu-id="6709b-774">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="6709b-774">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6709b-775">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="6709b-775">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6709b-776">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="6709b-776">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6709b-777">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="6709b-777">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6709b-778">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="6709b-778">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6709b-779">El middleware de filtrado de host se facilita a través del paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o 2.2).</span><span class="sxs-lookup"><span data-stu-id="6709b-779">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6709b-780">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-780">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6709b-781">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-781">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6709b-782">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="6709b-782">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6709b-783">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="6709b-783">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6709b-784">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-784">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6709b-785">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="6709b-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6709b-786">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="6709b-786">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6709b-787">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="6709b-787">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6709b-788">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-788">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6709b-789">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6709b-789">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6709b-790">Kestrel es un [servidor web multiplataforma de ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-790">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6709b-791">Kestrel es el servidor web que se incluye de forma predeterminada en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-791">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6709b-792">Kestrel admite los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="6709b-792">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6709b-793">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6709b-793">HTTPS</span></span>
* <span data-ttu-id="6709b-794">Actualización opaca para habilitar [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6709b-794">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6709b-795">Sockets de Unix para alto rendimiento detrás de Nginx</span><span class="sxs-lookup"><span data-stu-id="6709b-795">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="6709b-796">Kestrel admite todas las plataformas y versiones que sean compatibles con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6709b-796">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6709b-797">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6709b-797">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6709b-798">Cuándo usar Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="6709b-798">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6709b-799">Kestrel se puede usar por sí solo o con un *servidor proxy inverso*, como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6709b-799">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6709b-800">Un servidor proxy inverso recibe las solicitudes HTTP de la red y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-800">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6709b-801">Kestrel empleado como servidor web perimetral (accesible desde Internet):</span><span class="sxs-lookup"><span data-stu-id="6709b-801">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6709b-803">Kestrel empleado en una configuración de proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="6709b-803">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6709b-805">Cualquiera de las configuraciones, con o sin servidor proxy inverso, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="6709b-805">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6709b-806">Kestrel, utilizado como un servidor perimetral sin un servidor proxy inverso, no permite compartir la misma dirección IP y el mismo puerto entre varios procesos.</span><span class="sxs-lookup"><span data-stu-id="6709b-806">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6709b-807">Si Kestrel se configura para escuchar en un puerto, controla todo el tráfico de ese puerto, independientemente de los encabezados `Host` de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-807">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6709b-808">Un proxy inverso que puede compartir puertos es capaz de reenviar solicitudes a Kestrel en una única dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-808">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6709b-809">Aunque no sea necesario un servidor proxy inverso, su uso puede ser útil.</span><span class="sxs-lookup"><span data-stu-id="6709b-809">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6709b-810">Un proxy inverso puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-810">A reverse proxy:</span></span>

* <span data-ttu-id="6709b-811">Limitar el área expuesta públicamente de las aplicaciones que hospeda.</span><span class="sxs-lookup"><span data-stu-id="6709b-811">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6709b-812">Proporcionar una capa extra de configuración y defensa.</span><span class="sxs-lookup"><span data-stu-id="6709b-812">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6709b-813">Posiblemente, integrarse mejor con la infraestructura existente.</span><span class="sxs-lookup"><span data-stu-id="6709b-813">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6709b-814">Simplificar el equilibrio de carga y la configuración de una comunicación segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-814">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6709b-815">Solamente el servidor proxy inverso necesita un certificado X.509 y dicho servidor se puede comunicar con los servidores de la aplicación en la red interna por medio de HTTP sin formato.</span><span class="sxs-lookup"><span data-stu-id="6709b-815">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-816">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-816">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6709b-817">Cómo usar Kestrel en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6709b-817">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6709b-818">El paquete [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6709b-818">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6709b-819">Las plantillas de proyecto de ASP.NET Core usan Kestrel de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-819">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6709b-820">En *Program.cs*, el código de plantilla llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que a su vez llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="6709b-820">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="6709b-821">Para proporcionar configuración adicional después de llamar a `CreateDefaultBuilder`, llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-821">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6709b-822">Para más información sobre `CreateDefaultBuilder` y la compilación del host, consulte la sección sobre *cómo configurar un host* de <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="6709b-822">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="6709b-823">Opciones de Kestrel</span><span class="sxs-lookup"><span data-stu-id="6709b-823">Kestrel options</span></span>

<span data-ttu-id="6709b-824">El servidor web Kestrel tiene opciones de configuración de restricción que son especialmente útiles en las implementaciones con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="6709b-824">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6709b-825">Establezca restricciones en la propiedad <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-825">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6709b-826">La propiedad `Limits` contiene una instancia de la clase <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="6709b-826">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6709b-827">En los ejemplos siguientes se usa el espacio de nombres <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="6709b-827">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6709b-828">Las opciones de Kestrel, que se configuran en código de C# en los siguientes ejemplos, también se pueden establecer mediante un [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-828">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6709b-829">Por ejemplo, el proveedor de configuración de archivo puede cargar la configuración de Kestrel desde un archivo *appsettings.json* o *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-829">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="6709b-830">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="6709b-830">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6709b-831">Configure Kestrel en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6709b-831">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6709b-832">Inserte una instancia de `IConfiguration` en la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6709b-832">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6709b-833">En el ejemplo siguiente se da por supuesto que la configuración insertada está asignada a la propiedad `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="6709b-833">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6709b-834">En `Startup.ConfigureServices`, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-834">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6709b-835">Configure Kestrel al compilar el host:</span><span class="sxs-lookup"><span data-stu-id="6709b-835">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6709b-836">En *Program.cs*, cargue la sección de configuración `Kestrel` en la configuración de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6709b-836">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="6709b-837">Los dos procedimientos anteriores funcionan con cualquier [proveedor de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6709b-837">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6709b-838">Tiempo de expiración de la conexión persistente</span><span class="sxs-lookup"><span data-stu-id="6709b-838">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6709b-839">Obtiene o establece el [tiempo de expiración de la conexión persistente](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6709b-839">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6709b-840">El valor predeterminado es de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="6709b-840">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="6709b-841">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="6709b-841">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6709b-842">El número máximo de conexiones de TCP abiertas simultáneas que se pueden establecer para toda la aplicación con este código:</span><span class="sxs-lookup"><span data-stu-id="6709b-842">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="6709b-843">Hay un límite independiente para las conexiones que se han actualizado desde HTTP o HTTPS a otro protocolo (por ejemplo, en una solicitud de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6709b-843">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6709b-844">Cuando se actualiza una conexión, no se cuenta con respecto al límite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="6709b-844">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="6709b-845">El número máximo de conexiones es ilimitado de forma predeterminada (null).</span><span class="sxs-lookup"><span data-stu-id="6709b-845">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6709b-846">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-846">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6709b-847">El tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6709b-847">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6709b-848">El método recomendado para invalidar el límite de una aplicación ASP.NET Core MVC es usar el atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="6709b-848">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6709b-849">Este es un ejemplo que muestra cómo configurar la restricción en la aplicación y todas las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="6709b-849">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="6709b-850">Invalide la configuración en una solicitud específica de middleware:</span><span class="sxs-lookup"><span data-stu-id="6709b-850">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6709b-851">Se inicia una excepción si la aplicación configura el límite de una solicitud después de que la aplicación haya empezado a leer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-851">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6709b-852">Hay una propiedad `IsReadOnly` que señala si la propiedad `MaxRequestBodySize` tiene el estado de solo lectura, lo que significa que es demasiado tarde para configurar el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-852">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6709b-853">Cuando se ejecuta una aplicación [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás del [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module), el límite de tamaño del cuerpo de la solicitud de Kestrel se deshabilita porque IIS ya establece el límite.</span><span class="sxs-lookup"><span data-stu-id="6709b-853">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6709b-854">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-854">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6709b-855">Kestrel comprueba cada segundo si los datos entran a la velocidad especificada en bytes por segundo.</span><span class="sxs-lookup"><span data-stu-id="6709b-855">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6709b-856">Si la velocidad está por debajo del mínimo, se agota el tiempo de espera de la conexión. El período de gracia es la cantidad de tiempo que Kestrel da al cliente para aumentar su velocidad de envío hasta el mínimo; no se comprueba la velocidad durante ese tiempo.</span><span class="sxs-lookup"><span data-stu-id="6709b-856">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6709b-857">Este período de gracia permite evitar que se interrumpan las conexiones que inicialmente están enviando datos a una velocidad lenta debido a un inicio lento de TCP.</span><span class="sxs-lookup"><span data-stu-id="6709b-857">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6709b-858">La velocidad mínima predeterminada es 240 bytes por segundo, con un período de gracia de cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-858">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6709b-859">También se aplica una velocidad mínima a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-859">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6709b-860">El código para establecer el límite de solicitudes y el límite de respuestas es el mismo, salvo que tienen `RequestBody` o `Response` en los nombres de propiedad y de interfaz.</span><span class="sxs-lookup"><span data-stu-id="6709b-860">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6709b-861">Este es un ejemplo que muestra cómo configurar las velocidades de datos mínimas en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6709b-861">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="6709b-862">Tiempo de expiración de los encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="6709b-862">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6709b-863">Obtiene o establece la cantidad máxima de tiempo que el servidor pasa recibiendo las cabeceras de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6709b-863">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6709b-864">El valor predeterminado es 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="6709b-864">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="6709b-865">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="6709b-865">Synchronous I/O</span></span>

<span data-ttu-id="6709b-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla si se permite la E/S sincrónica para la solicitud y la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6709b-867">El valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="6709b-867">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6709b-868">Un gran número de operaciones de E/S sincrónicas de bloqueo puede dar lugar al colapso del grupo de subprocesos, lo que hace que la aplicación no responda.</span><span class="sxs-lookup"><span data-stu-id="6709b-868">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6709b-869">Habilite solo `AllowSynchronousIO` al usar una biblioteca que no admite la E/S asincrónica.</span><span class="sxs-lookup"><span data-stu-id="6709b-869">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6709b-870">En el ejemplo siguiente se deshabilita la E/S sincrónica:</span><span class="sxs-lookup"><span data-stu-id="6709b-870">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="6709b-871">Para más información sobre otras opciones y límites de Kestrel, vea:</span><span class="sxs-lookup"><span data-stu-id="6709b-871">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6709b-872">Configuración de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="6709b-872">Endpoint configuration</span></span>

<span data-ttu-id="6709b-873">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="6709b-873">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6709b-874">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="6709b-874">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6709b-875">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="6709b-875">Specify URLs using the:</span></span>

* <span data-ttu-id="6709b-876">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="6709b-876">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6709b-877">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-877">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6709b-878">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-878">`urls` host configuration key.</span></span>
* <span data-ttu-id="6709b-879">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-879">`UseUrls` extension method.</span></span>

<span data-ttu-id="6709b-880">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-880">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6709b-881">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6709b-881">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6709b-882">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6709b-882">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6709b-883">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="6709b-883">A development certificate is created:</span></span>

* <span data-ttu-id="6709b-884">Cuando el [SDK de .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="6709b-884">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6709b-885">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="6709b-885">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6709b-886">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="6709b-886">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6709b-887">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6709b-887">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6709b-888">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-888">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6709b-889">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6709b-889">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6709b-890">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="6709b-890">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6709b-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6709b-892">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-892">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6709b-893">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-893">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="6709b-894">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6709b-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6709b-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6709b-896">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-896">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6709b-897">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="6709b-897">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="6709b-898">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="6709b-898">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="6709b-899">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6709b-899">Configure(IConfiguration)</span></span>

<span data-ttu-id="6709b-900">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="6709b-900">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6709b-901">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-901">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6709b-902">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6709b-902">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6709b-903">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6709b-903">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6709b-904">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-904">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6709b-905">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-905">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6709b-906">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="6709b-906">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="6709b-907">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="6709b-907">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6709b-908">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6709b-908">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6709b-909">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-909">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6709b-910">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-910">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6709b-911">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="6709b-911">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6709b-912">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-912">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6709b-913">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-913">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6709b-914">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="6709b-914">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6709b-915">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="6709b-915">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6709b-916">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="6709b-916">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6709b-917">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-917">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6709b-918">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6709b-918">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6709b-919">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="6709b-919">Supported configurations described next:</span></span>

* <span data-ttu-id="6709b-920">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-920">No configuration</span></span>
* <span data-ttu-id="6709b-921">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="6709b-921">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6709b-922">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="6709b-922">Change the defaults in code</span></span>

<span data-ttu-id="6709b-923">*Sin configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-923">*No configuration*</span></span>

<span data-ttu-id="6709b-924">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="6709b-924">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6709b-925">*Reemplazar el certificado predeterminado de configuración*</span><span class="sxs-lookup"><span data-stu-id="6709b-925">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6709b-926">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-926">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6709b-927">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-927">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6709b-928">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-928">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6709b-929">En el ejemplo *appsettings.json* siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-929">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6709b-930">Establezca **AllowInvalid** en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="6709b-930">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6709b-931">Cualquier punto de conexión HTTPS que no especifique un certificado (**HttpsDefaultCert** en el siguiente ejemplo) revierte al certificado definido en **Certificados** > **Predeterminado** o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="6709b-931">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="6709b-932">Una alternativa al uso de **Path** y **Password** en cualquier nodo de certificado consiste en especificar el certificado por medio de campos del almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="6709b-932">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6709b-933">Por ejemplo, el certificado en **Certificados** > **Predeterminado** se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="6709b-933">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6709b-934">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="6709b-934">Schema notes:</span></span>

* <span data-ttu-id="6709b-935">En los nombres de los puntos de conexión se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6709b-935">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6709b-936">Por ejemplo, `HTTPS` y `Https` son válidos.</span><span class="sxs-lookup"><span data-stu-id="6709b-936">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6709b-937">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-937">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6709b-938">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="6709b-938">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6709b-939">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="6709b-939">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6709b-940">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-940">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6709b-941">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="6709b-941">The `Certificate` section is optional.</span></span> <span data-ttu-id="6709b-942">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en escenarios anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-942">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6709b-943">Si no hay valores predeterminados disponibles, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="6709b-943">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6709b-944">La sección `Certificate` admite certificadostanto **Path**&ndash;**Password** como **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="6709b-944">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6709b-945">Se puede definir el número de puntos de conexión que se quiera de esta manera, siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-945">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6709b-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un `KestrelConfigurationLoader` con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="6709b-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="6709b-947">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="6709b-947">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6709b-948">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="6709b-948">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6709b-949">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="6709b-949">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6709b-950">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="6709b-950">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6709b-951">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="6709b-951">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6709b-952">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="6709b-952">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6709b-953">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="6709b-953">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6709b-954">*Cambiar los valores predeterminados en el código*</span><span class="sxs-lookup"><span data-stu-id="6709b-954">*Change the defaults in code*</span></span>

<span data-ttu-id="6709b-955">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-955">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6709b-956">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-956">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="6709b-957">*Compatibilidad de Kestrel con SNI*</span><span class="sxs-lookup"><span data-stu-id="6709b-957">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6709b-958">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="6709b-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6709b-959">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="6709b-959">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6709b-960">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-960">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6709b-961">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="6709b-961">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6709b-962">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="6709b-962">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6709b-963">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6709b-963">SNI support requires:</span></span>

* <span data-ttu-id="6709b-964">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="6709b-964">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6709b-965">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="6709b-965">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6709b-966">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="6709b-966">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6709b-967">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-967">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6709b-968">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="6709b-968">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="6709b-969">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="6709b-969">Connection logging</span></span>

<span data-ttu-id="6709b-970">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-970">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6709b-971">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="6709b-971">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6709b-972">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-972">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6709b-973">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="6709b-973">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6709b-974">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="6709b-974">Bind to a TCP socket</span></span>

<span data-ttu-id="6709b-975">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="6709b-975">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="6709b-976">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="6709b-976">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6709b-977">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="6709b-977">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6709b-978">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="6709b-978">Bind to a Unix socket</span></span>

<span data-ttu-id="6709b-979">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6709b-979">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="6709b-980">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="6709b-980">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6709b-981">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="6709b-981">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6709b-982">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="6709b-982">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="6709b-983">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="6709b-983">Port 0</span></span>

<span data-ttu-id="6709b-984">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="6709b-984">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6709b-985">En el siguiente ejemplo se muestra cómo averiguar qué puerto Kestrel está realmente enlazado a un runtime:</span><span class="sxs-lookup"><span data-stu-id="6709b-985">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6709b-986">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-986">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6709b-987">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="6709b-987">Limitations</span></span>

<span data-ttu-id="6709b-988">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="6709b-988">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6709b-989">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-989">`--urls` command-line argument</span></span>
* <span data-ttu-id="6709b-990">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="6709b-990">`urls` host configuration key</span></span>
* <span data-ttu-id="6709b-991">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="6709b-991">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6709b-992">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6709b-992">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6709b-993">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="6709b-993">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6709b-994">HTTPS no se puede usar con estos métodos, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, por medio de la configuración `KestrelServerOptions` o de un archivo de configuración, tal y como se explicó anteriormente en este tema).</span><span class="sxs-lookup"><span data-stu-id="6709b-994">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6709b-995">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-995">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6709b-996">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="6709b-996">IIS endpoint configuration</span></span>

<span data-ttu-id="6709b-997">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-997">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6709b-998">Para más información, vea el tema [Módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="6709b-998">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="6709b-999">Configuración de transporte de Libuv</span><span class="sxs-lookup"><span data-stu-id="6709b-999">Libuv transport configuration</span></span>

<span data-ttu-id="6709b-1000">Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados.</span><span class="sxs-lookup"><span data-stu-id="6709b-1000">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6709b-1001">Se trata de un cambio muy importante para las aplicaciones ASP.NET Core 2.0 que actualizan a 2.1 y llaman a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>, y que dependen de cualquiera de los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="6709b-1001">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6709b-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referencia de paquete directa)</span><span class="sxs-lookup"><span data-stu-id="6709b-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6709b-1003">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6709b-1003">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6709b-1004">Para los proyectos que necesitan el uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="6709b-1004">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6709b-1005">Agregar una dependencia del paquete [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6709b-1005">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6709b-1006">Llame a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-1006">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="6709b-1007">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="6709b-1007">URL prefixes</span></span>

<span data-ttu-id="6709b-1008">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="6709b-1008">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6709b-1009">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="6709b-1009">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6709b-1010">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="6709b-1010">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6709b-1011">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-1011">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6709b-1012">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-1012">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6709b-1013">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-1013">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6709b-1014">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-1014">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6709b-1015">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-1015">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6709b-1016">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="6709b-1016">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6709b-1017">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="6709b-1017">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6709b-1018">Para enlazar distintos nombres de host a distintas aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso, como IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="6709b-1018">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6709b-1019">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6709b-1019">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6709b-1020">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="6709b-1020">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6709b-1021">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="6709b-1021">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6709b-1022">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="6709b-1022">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6709b-1023">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="6709b-1023">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6709b-1024">Filtrado de hosts</span><span class="sxs-lookup"><span data-stu-id="6709b-1024">Host filtering</span></span>

<span data-ttu-id="6709b-1025">Si bien Kestrel admite una configuración basada en prefijos como `http://example.com:5000`, pasa por alto completamente el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="6709b-1025">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6709b-1026">El host `localhost` es un caso especial que se usa para enlazar a direcciones de bucle invertido.</span><span class="sxs-lookup"><span data-stu-id="6709b-1026">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6709b-1027">Cualquier otro host que no sea una dirección IP explícita se enlaza a todas las direcciones IP públicas.</span><span class="sxs-lookup"><span data-stu-id="6709b-1027">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6709b-1028">Los encabezados `Host` no están validados.</span><span class="sxs-lookup"><span data-stu-id="6709b-1028">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6709b-1029">Como solución alternativa, use el Middleware de filtrado de hosts.</span><span class="sxs-lookup"><span data-stu-id="6709b-1029">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6709b-1030">El middleware de filtrado de host se facilita a través del paquete [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o 2.2).</span><span class="sxs-lookup"><span data-stu-id="6709b-1030">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6709b-1031">El middleware se agrega por medio de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que llama a <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="6709b-1031">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6709b-1032">El Middleware de filtrado de hosts está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6709b-1032">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6709b-1033">Para habilitarlo, defina una clave `AllowedHosts` en *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="6709b-1033">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6709b-1034">El valor es una lista delimitada por punto y coma de nombres de host sin los números de puerto:</span><span class="sxs-lookup"><span data-stu-id="6709b-1034">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6709b-1035">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6709b-1035">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6709b-1036">[Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) también tiene una opción <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="6709b-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6709b-1037">El Middleware de encabezados reenviados y el Middleware de filtrado de hosts tienen una funcionalidad similar en diferentes escenarios.</span><span class="sxs-lookup"><span data-stu-id="6709b-1037">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6709b-1038">Establecer `AllowedHosts` con el Middleware de encabezados reenviados es adecuado cuando el encabezado `Host` no se conserva mientras se reenvían solicitudes con un servidor proxy inverso o un equilibrador de carga.</span><span class="sxs-lookup"><span data-stu-id="6709b-1038">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6709b-1039">Establecer `AllowedHosts` con el Middleware de filtrado de hosts es adecuado cuando se usa Kestrel como un servidor perimetral de acceso público o cuando el encabezado `Host` se reenvía directamente.</span><span class="sxs-lookup"><span data-stu-id="6709b-1039">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6709b-1040">Para obtener más información sobre el Middleware de encabezados reenviados, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6709b-1040">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="6709b-1041">Vaciado de solicitudes HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="6709b-1041">HTTP/1.1 request draining</span></span>

<span data-ttu-id="6709b-1042">La apertura de conexiones HTTP lleva mucho tiempo.</span><span class="sxs-lookup"><span data-stu-id="6709b-1042">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="6709b-1043">En HTTPS, además consume muchos recursos.</span><span class="sxs-lookup"><span data-stu-id="6709b-1043">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="6709b-1044">Por lo tanto, Kestrel intenta reutilizar las conexiones conforme al protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6709b-1044">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="6709b-1045">El cuerpo de una solicitud debe usarse por completo para que se vuelva a usar la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-1045">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="6709b-1046">La aplicación no siempre consume el cuerpo de la solicitud, por ejemplo, `POST` solicita dónde devuelve un redireccionamiento o una respuesta 404 el servidor.</span><span class="sxs-lookup"><span data-stu-id="6709b-1046">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="6709b-1047">En el caso de un redireccionamiento `POST`:</span><span class="sxs-lookup"><span data-stu-id="6709b-1047">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="6709b-1048">Es posible que el cliente ya haya enviado parte de los datos de `POST`.</span><span class="sxs-lookup"><span data-stu-id="6709b-1048">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="6709b-1049">El servidor escribe la respuesta 301.</span><span class="sxs-lookup"><span data-stu-id="6709b-1049">The server writes the 301 response.</span></span>
* <span data-ttu-id="6709b-1050">La conexión no se puede usar para una nueva solicitud hasta que los datos de `POST` del cuerpo de la solicitud anterior se han leído en su totalidad.</span><span class="sxs-lookup"><span data-stu-id="6709b-1050">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="6709b-1051">Kestrel intenta vaciar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-1051">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="6709b-1052">El vaciado del cuerpo de la solicitud significa leer y descartar los datos sin procesarlos.</span><span class="sxs-lookup"><span data-stu-id="6709b-1052">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="6709b-1053">El proceso de vaciado llega a un equilibrio entre permitir que se vuelva a usar la conexión y el tiempo que se tarda en vaciar los datos restantes:</span><span class="sxs-lookup"><span data-stu-id="6709b-1053">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="6709b-1054">El vaciado tiene un tiempo de espera de cinco segundos que no se puede configurar.</span><span class="sxs-lookup"><span data-stu-id="6709b-1054">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="6709b-1055">Si no se han leído todos los datos especificados por el encabezado `Content-Length` o `Transfer-Encoding` antes de que se agote el tiempo de espera, se cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-1055">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="6709b-1056">A veces, puede que quiera finalizar la solicitud inmediatamente, antes o después de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-1056">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="6709b-1057">Por ejemplo, los clientes pueden tener límites de datos restrictivos, así que limitar los datos cargados podría ser una prioridad.</span><span class="sxs-lookup"><span data-stu-id="6709b-1057">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="6709b-1058">En tales casos, para finalizar una solicitud, llame a [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) desde un controlador, una página de Razor o un middleware.</span><span class="sxs-lookup"><span data-stu-id="6709b-1058">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="6709b-1059">Hay advertencias respecto a la llamada a `Abort`:</span><span class="sxs-lookup"><span data-stu-id="6709b-1059">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="6709b-1060">La creación de nuevas conexiones puede ser lenta y costosa.</span><span class="sxs-lookup"><span data-stu-id="6709b-1060">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="6709b-1061">No hay ninguna garantía de que el cliente haya leído la respuesta antes de que se cierre la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-1061">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="6709b-1062">La llamada a `Abort` debe ser poco frecuente y reservada para casos de error graves, no comunes.</span><span class="sxs-lookup"><span data-stu-id="6709b-1062">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="6709b-1063">Llame a `Abort` solo cuando sea necesario resolver un problema concreto.</span><span class="sxs-lookup"><span data-stu-id="6709b-1063">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="6709b-1064">Por ejemplo, llame a `Abort` si hay clientes malintencionados intentando aplicar `POST` a los datos o si hay un error en el código del cliente que produce muchas solicitudes o de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="6709b-1064">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="6709b-1065">No llame a `Abort` para situaciones de error comunes, como HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="6709b-1065">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="6709b-1066">La llamada a [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes de llamar a `Abort` garantiza que el servidor haya terminado de escribir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6709b-1066">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="6709b-1067">Pero el comportamiento del cliente no es predecible y es posible que no lea la respuesta antes de que se anule la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-1067">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="6709b-1068">Este proceso es diferente para HTTP/2, ya que el protocolo admite la anulación de flujos de solicitud individuales sin cerrar la conexión.</span><span class="sxs-lookup"><span data-stu-id="6709b-1068">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="6709b-1069">El tiempo de espera de vaciado de cinco segundos no se aplica.</span><span class="sxs-lookup"><span data-stu-id="6709b-1069">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="6709b-1070">Si hay datos del cuerpo de la solicitud sin leer después de completar una respuesta, el servidor envía un marco RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6709b-1070">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="6709b-1071">Se omiten los marcos de datos del cuerpo de la solicitud adicionales.</span><span class="sxs-lookup"><span data-stu-id="6709b-1071">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="6709b-1072">Si es posible, es mejor que los clientes usen el encabezado de solicitud [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) y esperen a que el servidor responda para empezar a enviar el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6709b-1072">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="6709b-1073">Esto proporciona al cliente la oportunidad de examinar la respuesta y anular antes de enviar datos innecesarios.</span><span class="sxs-lookup"><span data-stu-id="6709b-1073">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6709b-1074">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6709b-1074">Additional resources</span></span>

* <span data-ttu-id="6709b-1075">Al usar sockets de UNIX en Linux, el socket no se elimina de forma automática al apagar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6709b-1075">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="6709b-1076">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="6709b-1076">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="6709b-1077">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4) (RFC 7230: Enrutamiento y sintaxis de mensajes [Sección 5.4: Host])</span><span class="sxs-lookup"><span data-stu-id="6709b-1077">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)</span></span>
