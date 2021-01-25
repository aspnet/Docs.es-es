---
title: Hospedar ASP.NET Core en Linux con Nginx
author: rick-anderson
description: Obtenga información sobre cómo configurar Nginx como un proxy inverso en Ubuntu 16.04 para reenviar el tráfico HTTP a una aplicación web de ASP.NET Core que se ejecuta en Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 6a8fd8e3498dda9b7c10834791e64df6276e2823
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253025"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="0684c-103">Hospedar ASP.NET Core en Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="0684c-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="0684c-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="0684c-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="0684c-105">En esta guía se explica cómo configurar un entorno de ASP.NET Core listo para producción en un servidor de Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="0684c-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="0684c-106">Probablemente estas instrucciones sean válidas también con versiones más recientes de Ubuntu, pero no se han probado con versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="0684c-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="0684c-107">Para información sobre otras distribuciones de Linux compatibles con ASP.NET Core, consulte [Requisitos previos para .NET Core en Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="0684c-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="0684c-108">En Ubuntu 14.04, `supervisord` is es la solución recomendada para supervisar el proceso de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0684c-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="0684c-109">`systemd` no está disponible en Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="0684c-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="0684c-110">Para obtener instrucciones de Ubuntu 14.04, vea la [versión anterior de este tema](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="0684c-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="0684c-111">En esta guía:</span><span class="sxs-lookup"><span data-stu-id="0684c-111">This guide:</span></span>

* <span data-ttu-id="0684c-112">Se coloca una aplicación ASP.NET Core existente detrás de un servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="0684c-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="0684c-113">Se configura el servidor proxy inverso para reenviar las solicitudes al servidor web de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0684c-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="0684c-114">Se garantiza que la aplicación web se ejecute al inicio como un demonio.</span><span class="sxs-lookup"><span data-stu-id="0684c-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="0684c-115">Se configura una herramienta de administración de procesos para ayudar a reiniciar la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0684c-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0684c-116">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="0684c-116">Prerequisites</span></span>

1. <span data-ttu-id="0684c-117">Acceso a un servidor de Ubuntu 16.04 con una cuenta de usuario estándar con privilegios sudo.</span><span class="sxs-lookup"><span data-stu-id="0684c-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="0684c-118">Tener .NET Core Runtime instalado en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="0684c-119">Visite la página [Descargar .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="0684c-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="0684c-120">Seleccione la versión más reciente de .NET Core que no sea de versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="0684c-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="0684c-121">Descargue el runtime más reciente que no sea de versión preliminar en la tabla en **Ejecutar aplicaciones: runtime**.</span><span class="sxs-lookup"><span data-stu-id="0684c-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="0684c-122">Seleccione el vínculo **Instrucciones del administrador de paquetes** de Linux y siga las instrucciones de Ubuntu para su versión de Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="0684c-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="0684c-123">Disponer de una aplicación de ASP.NET Core existente.</span><span class="sxs-lookup"><span data-stu-id="0684c-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="0684c-124">En cualquier momento futuro tras actualizar el marco de trabajo compartido, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="0684c-125">Publicar y copiar en la aplicación</span><span class="sxs-lookup"><span data-stu-id="0684c-125">Publish and copy over the app</span></span>

<span data-ttu-id="0684c-126">Configure la aplicación para una [implementación dependiente de Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="0684c-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="0684c-127">Si la aplicación se ejecuta localmente y no está configurada para realizar conexiones seguras (HTTPS), adopte cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0684c-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="0684c-128">Configure la aplicación para controlar las conexiones locales seguras.</span><span class="sxs-lookup"><span data-stu-id="0684c-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="0684c-129">Para obtener más información, vea la sección [Configuración de HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="0684c-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="0684c-130">Quite `https://localhost:5001` (si existe) de la propiedad `applicationUrl` en el archivo `Properties/launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="0684c-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="0684c-131">Ejecute [dotnet publish](/dotnet/core/tools/dotnet-publish) desde el entorno de desarrollo para empaquetar una aplicación en un directorio (por ejemplo, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, donde el marcador de posición `{TARGET FRAMEWORK MONIKER}` es el moniker de la plataforma de destino, o TFM) que se pueda ejecutar en el servidor:</span><span class="sxs-lookup"><span data-stu-id="0684c-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="0684c-132">La aplicación también se puede publicar como una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd) si prefiere no mantener .NET Core Runtime en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="0684c-133">Copie la aplicación de ASP.NET Core en el servidor usando una herramienta que se integre en el flujo de trabajo de la organización (como `SCP` o `SFTP`).</span><span class="sxs-lookup"><span data-stu-id="0684c-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="0684c-134">Es habitual encontrar las aplicaciones web en el directorio `var` (por ejemplo, `var/www/helloapp`).</span><span class="sxs-lookup"><span data-stu-id="0684c-134">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="0684c-135">En un escenario de implementación de producción, un flujo de trabajo de integración continua lleva a cabo la tarea de publicar la aplicación y copiar los recursos en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="0684c-136">Pruebe la aplicación:</span><span class="sxs-lookup"><span data-stu-id="0684c-136">Test the app:</span></span>

1. <span data-ttu-id="0684c-137">Desde la línea de comandos, ejecute la aplicación: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="0684c-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="0684c-138">En un explorador, vaya a `http://<serveraddress>:<port>` para comprobar que la aplicación funciona en Linux de forma local.</span><span class="sxs-lookup"><span data-stu-id="0684c-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="0684c-139">Configurar un servidor proxy inverso</span><span class="sxs-lookup"><span data-stu-id="0684c-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="0684c-140">Un proxy inverso es una configuración común para trabajar con aplicaciones web dinámicas.</span><span class="sxs-lookup"><span data-stu-id="0684c-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="0684c-141">Un proxy inverso finaliza la solicitud HTTP y la reenvía a la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0684c-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="0684c-142">Usar un servidor proxy inverso</span><span class="sxs-lookup"><span data-stu-id="0684c-142">Use a reverse proxy server</span></span>

<span data-ttu-id="0684c-143">Kestrel resulta muy adecuado para suministrar contenido dinámico de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0684c-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="0684c-144">Sin embargo, las funcionalidades de servicio web no son tan completas como las de los servidores, como IIS, Apache o Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="0684c-145">Un servidor proxy inverso puede reducir las cargas de trabajo, por ejemplo, suministrar contenido estático, almacenar solicitudes en caché, comprimir solicitudes y finalizar HTTPS desde el servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="0684c-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="0684c-146">Un servidor proxy inverso puede residir en un equipo dedicado o se puede implementar junto con un servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="0684c-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="0684c-147">Para los fines de esta guía, se usa una única instancia de Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="0684c-148">Se ejecuta en el mismo servidor, junto con el servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="0684c-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="0684c-149">En función de requisitos, se puede elegir una configuración diferente.</span><span class="sxs-lookup"><span data-stu-id="0684c-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="0684c-150">Dado que el proxy inverso reenvía las solicitudes, use el [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) del paquete [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides).</span><span class="sxs-lookup"><span data-stu-id="0684c-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="0684c-151">El middleware actualiza `Request.Scheme`, mediante el encabezado `X-Forwarded-Proto`, para que los URI de redireccionamiento y otras directivas de seguridad funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="0684c-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="0684c-152">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> que está al principio de `Startup.Configure` antes de llamar a otro middleware.</span><span class="sxs-lookup"><span data-stu-id="0684c-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="0684c-153">Configure el middleware para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="0684c-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="0684c-154">Si no se especifica ningún valor <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para el middleware, los encabezados predeterminados para reenviar son `None`.</span><span class="sxs-lookup"><span data-stu-id="0684c-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="0684c-155">Los servidores proxy que se ejecutan en direcciones de bucle invertido (`127.0.0.0/8` o `[::1]`), incluida la dirección de localhost (`127.0.0.1`) estándar, son de confianza de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="0684c-155">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="0684c-156">Si otras redes o servidores proxy de confianza de la organización tramitan solicitudes entre Internet y el servidor web, agréguelos a la lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="0684c-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="0684c-157">En el ejemplo siguiente se agrega un servidor proxy de confianza en la dirección IP 10.0.0.100 al middleware de encabezados reenviados `KnownProxies` en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0684c-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="0684c-158">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="0684c-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="0684c-159">Instalar Nginx</span><span class="sxs-lookup"><span data-stu-id="0684c-159">Install Nginx</span></span>

<span data-ttu-id="0684c-160">Use `apt-get` para instalar Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="0684c-161">El instalador crea un script de inicio `systemd` que ejecuta Nginx como demonio al iniciarse el sistema.</span><span class="sxs-lookup"><span data-stu-id="0684c-161">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="0684c-162">Siga las instrucciones de instalación para Ubuntu en [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: paquetes oficiales de Debian y Ubuntu).</span><span class="sxs-lookup"><span data-stu-id="0684c-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="0684c-163">Si se necesitan módulos de Nginx opcionales, puede que haya que compilar Nginx desde el origen.</span><span class="sxs-lookup"><span data-stu-id="0684c-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="0684c-164">Puesto que Nginx se ha instalado por primera vez, ejecute lo siguiente para iniciarlo de forma explícita:</span><span class="sxs-lookup"><span data-stu-id="0684c-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="0684c-165">Compruebe que un explorador muestra la página de aterrizaje predeterminada de Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="0684c-166">La página de aterrizaje está accesible en `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="0684c-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="0684c-167">Configurar Nginx</span><span class="sxs-lookup"><span data-stu-id="0684c-167">Configure Nginx</span></span>

<span data-ttu-id="0684c-168">Para configurar Nginx como un proxy inverso a fin de que reenvíe solicitudes HTTP a nuestra aplicación de ASP.NET Core, modifique `/etc/nginx/sites-available/default`.</span><span class="sxs-lookup"><span data-stu-id="0684c-168">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="0684c-169">Ábralo en un editor de texto y reemplace el contenido por el fragmento de contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="0684c-169">Open it in a text editor, and replace the contents with the following snippet:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="0684c-170">Si la aplicación es una aplicación SignalR o Blazor Server, vea <xref:signalr/scale#linux-with-nginx> y <xref:blazor/host-and-deploy/server#linux-with-nginx> respectivamente para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="0684c-170">If the app is a SignalR or Blazor Server app, see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="0684c-171">Cuando no hay ninguna coincidencia de `server_name`, Nginx usa el servidor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0684c-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="0684c-172">Si no se define ningún servidor predeterminado, el primer servidor del archivo de configuración es el servidor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0684c-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="0684c-173">Como procedimiento recomendado, agregue un servidor predeterminado específico que devuelva un código de estado de 444 en el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="0684c-173">As a best practice, add a specific default server that returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="0684c-174">Un ejemplo de configuración del servidor predeterminado es:</span><span class="sxs-lookup"><span data-stu-id="0684c-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0684c-175">Con el archivo de configuración anterior y el servidor predeterminado, Nginx acepta tráfico público en el puerto 80 con el encabezado de host `example.com` o `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="0684c-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="0684c-176">Las solicitudes que no coincidan con estos hosts no se reenviarán al Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0684c-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="0684c-177">Nginx reenvía las solicitudes coincidentes con Kestrel a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="0684c-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="0684c-178">Para obtener más información, vea [este artículo sobre cómo Nginx procesa una solicitud](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="0684c-178">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="0684c-179">Para cambiar la IP o el puerto de Kestrel, vea [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (Kestrel: configuración de los puntos de conexión).</span><span class="sxs-lookup"><span data-stu-id="0684c-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0684c-180">Con el archivo de configuración anterior y el servidor predeterminado, Nginx acepta tráfico público en el puerto 80 con el encabezado de host `example.com` o `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="0684c-180">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="0684c-181">Las solicitudes que no coincidan con estos hosts no se reenviarán al Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0684c-181">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="0684c-182">Nginx reenvía las solicitudes coincidentes con Kestrel a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="0684c-182">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="0684c-183">Para obtener más información, vea [este artículo sobre cómo Nginx procesa una solicitud](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="0684c-183">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="0684c-184">Para cambiar la IP o el puerto de Kestrel, vea [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (Kestrel: configuración de los puntos de conexión).</span><span class="sxs-lookup"><span data-stu-id="0684c-184">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="0684c-185">Si no se especifica una [directiva de server_name](https://nginx.org/docs/http/server_names.html) adecuada, su aplicación se expone a vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="0684c-185">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="0684c-186">Los enlaces de carácter comodín de subdominio (por ejemplo, `*.example.com`) no presentan este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable).</span><span class="sxs-lookup"><span data-stu-id="0684c-186">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="0684c-187">Para obtener más información, vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="0684c-187">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="0684c-188">Una vez establecida la configuración de Nginx, ejecute `sudo nginx -t` para comprobar la sintaxis de los archivos de configuración.</span><span class="sxs-lookup"><span data-stu-id="0684c-188">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="0684c-189">Si la prueba del archivo de configuración es correcta, fuerce a Nginx a recopilar los cambios mediante la ejecución de `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="0684c-189">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="0684c-190">Para ejecutar la aplicación directamente en el servidor:</span><span class="sxs-lookup"><span data-stu-id="0684c-190">To directly run the app on the server:</span></span>

1. <span data-ttu-id="0684c-191">Vaya al directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-191">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="0684c-192">Ejecute la aplicación: `dotnet <app_assembly.dll>`, donde `app_assembly.dll` es el nombre de archivo de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-192">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="0684c-193">Si la aplicación se ejecuta en el servidor, pero no responde a través de Internet, compruebe el firewall del servidor y confirme que el puerto 80 está abierto.</span><span class="sxs-lookup"><span data-stu-id="0684c-193">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm port 80 is open.</span></span> <span data-ttu-id="0684c-194">Si está usando una máquina virtual Ubuntu de Azure, agregue una regla de grupo de seguridad de red que posibilite el tráfico entrante del puerto 80.</span><span class="sxs-lookup"><span data-stu-id="0684c-194">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="0684c-195">No es necesario para habilitar una regla de tráfico saliente en el puerto 80, ya que dicho tráfico se concede automáticamente al habilitar la regla de entrada.</span><span class="sxs-lookup"><span data-stu-id="0684c-195">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="0684c-196">Cuando termine de probar la aplicación, ciérrela con <kbd>Ctrl</kbd> + <kbd>C</kbd> en el símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="0684c-196">When done testing the app, shut down the app with <kbd>Ctrl</kbd> + <kbd>C</kbd> at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="0684c-197">Supervisión de la aplicación</span><span class="sxs-lookup"><span data-stu-id="0684c-197">Monitor the app</span></span>

<span data-ttu-id="0684c-198">El servidor está configurado para reenviar las solicitudes realizadas a `http://<serveraddress>:80` a la aplicación de ASP.NET Core que se ejecuta en Kestrel en `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="0684c-198">The server is set up to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="0684c-199">Sin embargo, Nginx no está configurado para administrar el proceso de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0684c-199">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="0684c-200">Se puede usar `systemd` para crear un archivo de servicio para iniciar y supervisar la aplicación web subyacente.</span><span class="sxs-lookup"><span data-stu-id="0684c-200">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="0684c-201">`systemd` es un sistema de inicio que proporciona muchas características eficaces para iniciar, detener y administrar procesos.</span><span class="sxs-lookup"><span data-stu-id="0684c-201">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="0684c-202">Crear el archivo de servicio</span><span class="sxs-lookup"><span data-stu-id="0684c-202">Create the service file</span></span>

<span data-ttu-id="0684c-203">Cree el archivo de definición de servicio:</span><span class="sxs-lookup"><span data-stu-id="0684c-203">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="0684c-204">El ejemplo siguiente es un archivo de servicio para la aplicación:</span><span class="sxs-lookup"><span data-stu-id="0684c-204">The following example is a service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="0684c-205">En el ejemplo anterior, el usuario que administra el servicio se especifica mediante la opción `User`.</span><span class="sxs-lookup"><span data-stu-id="0684c-205">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="0684c-206">El usuario (`www-data`) debe existir y tener la propiedad correcta de los archivos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-206">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="0684c-207">Use `TimeoutStopSec` para configurar el período de tiempo que hay que esperar para que la aplicación se apague después de que reciba la señal de interrupción inicial.</span><span class="sxs-lookup"><span data-stu-id="0684c-207">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="0684c-208">Si la aplicación no se apaga en este período, se emite SIGKILL para terminar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-208">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="0684c-209">Proporcione el valor como segundos sin unidad (por ejemplo, `150`), un intervalo de tiempo (por ejemplo, `2min 30s`) o `infinity` para deshabilitar el tiempo de expiración.</span><span class="sxs-lookup"><span data-stu-id="0684c-209">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="0684c-210">El valor predeterminado de `TimeoutStopSec` es `DefaultTimeoutStopSec` en el archivo de configuración del administrador (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf` y `user.conf.d`).</span><span class="sxs-lookup"><span data-stu-id="0684c-210">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="0684c-211">El tiempo de expiración predeterminado para la mayoría de las distribuciones es de 90 segundos.</span><span class="sxs-lookup"><span data-stu-id="0684c-211">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="0684c-212">Linux tiene un sistema de archivos que distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0684c-212">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="0684c-213">Si se establece `ASPNETCORE_ENVIRONMENT` en `Production`, se busca en el archivo de configuración `appsettings.Production.json`, no en `appsettings.production.json`.</span><span class="sxs-lookup"><span data-stu-id="0684c-213">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="0684c-214">Algunos valores (por ejemplo, cadenas de conexión de SQL) deben ser de escape para que los proveedores de configuración lean las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="0684c-214">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="0684c-215">Use el siguiente comando para generar un valor de escape correctamente para su uso en el archivo de configuración:</span><span class="sxs-lookup"><span data-stu-id="0684c-215">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0684c-216">No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="0684c-216">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="0684c-217">Use un subrayado doble (`__`) en lugar de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="0684c-217">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="0684c-218">El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración.</span><span class="sxs-lookup"><span data-stu-id="0684c-218">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="0684c-219">En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="0684c-219">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0684c-220">No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="0684c-220">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="0684c-221">Use un subrayado doble (`__`) en lugar de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="0684c-221">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="0684c-222">El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración.</span><span class="sxs-lookup"><span data-stu-id="0684c-222">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="0684c-223">En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="0684c-223">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="0684c-224">Guarde el archivo y habilite el servicio.</span><span class="sxs-lookup"><span data-stu-id="0684c-224">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="0684c-225">Inicie el servicio y compruebe que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="0684c-225">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="0684c-226">Con el proxy inverso configurado y Kestrel administrado con `systemd`, la aplicación web está completamente configurada y se puede acceder a ella desde un explorador en la máquina local en `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="0684c-226">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="0684c-227">También es accesible desde una máquina remota, salvo que haya algún firewall que la pueda estar bloqueando.</span><span class="sxs-lookup"><span data-stu-id="0684c-227">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="0684c-228">Al inspeccionar los encabezados de respuesta, el encabezado `Server` muestra que Kestrel suministra la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0684c-228">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="0684c-229">Visualización de registros</span><span class="sxs-lookup"><span data-stu-id="0684c-229">View logs</span></span>

<span data-ttu-id="0684c-230">Dado que la aplicación web que usa Kestrel se administra mediante `systemd`, todos los procesos y eventos se registran en un diario centralizado.</span><span class="sxs-lookup"><span data-stu-id="0684c-230">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="0684c-231">En cambio, este diario incluye todas las entradas de todos los servicios y procesos administrados por `systemd`.</span><span class="sxs-lookup"><span data-stu-id="0684c-231">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="0684c-232">Para ver los elementos específicos de `kestrel-helloapp.service`, use el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0684c-232">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="0684c-233">Para aumentar el filtrado, las opciones de tiempo como `--since today` y `--until 1 hour ago`, o bien una combinación de estas, pueden reducir el número de entradas que se devuelven.</span><span class="sxs-lookup"><span data-stu-id="0684c-233">For further filtering, time options such as `--since today`, `--until 1 hour ago`, or a combination of these can reduce the number of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="0684c-234">Protección de datos</span><span class="sxs-lookup"><span data-stu-id="0684c-234">Data protection</span></span>

<span data-ttu-id="0684c-235">Hay varios [softwares intermedios](xref:fundamentals/middleware/index) de ASP.NET Core que utilizan la [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction), incluidos los de autenticación, como el de cookies, y las protecciones de falsificación de solicitud entre sitios (CSRF).</span><span class="sxs-lookup"><span data-stu-id="0684c-235">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="0684c-236">Aunque el código de usuario no llame a las API de protección de datos, esta se debe configurar para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente.</span><span class="sxs-lookup"><span data-stu-id="0684c-236">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="0684c-237">Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-237">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="0684c-238">Si el conjunto de claves se almacena en memoria cuando se reinicia la aplicación:</span><span class="sxs-lookup"><span data-stu-id="0684c-238">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="0684c-239">Todos los tokens de autenticación basados en cookie se invalidan.</span><span class="sxs-lookup"><span data-stu-id="0684c-239">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="0684c-240">Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud.</span><span class="sxs-lookup"><span data-stu-id="0684c-240">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="0684c-241">Ya no se pueden descifrar los datos protegidos con el conjunto de claves.</span><span class="sxs-lookup"><span data-stu-id="0684c-241">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="0684c-242">Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0684c-242">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="0684c-243">Para configurar la protección de datos de modo que sea persistente y permita cifrar el anillo de claves, consulte:</span><span class="sxs-lookup"><span data-stu-id="0684c-243">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="0684c-244">Campos del encabezado de solicitud más largos</span><span class="sxs-lookup"><span data-stu-id="0684c-244">Long request header fields</span></span>

<span data-ttu-id="0684c-245">La configuración predeterminada del servidor proxy normalmente limita los campos de encabezado de la solicitud a 4 K u 8 K, en función de la plataforma.</span><span class="sxs-lookup"><span data-stu-id="0684c-245">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="0684c-246">Una aplicación puede requerir campos con una longitud mayor que la predeterminada (por ejemplo, las aplicaciones que usan [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="0684c-246">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="0684c-247">Si se requieren campos más largos, es necesario ajustar la configuración predeterminada del servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="0684c-247">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="0684c-248">Los valores que se aplican dependen del escenario.</span><span class="sxs-lookup"><span data-stu-id="0684c-248">The values to apply depend on the scenario.</span></span> <span data-ttu-id="0684c-249">Para obtener más información, consulte la documentación del servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-249">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="0684c-250">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="0684c-250">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="0684c-251">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="0684c-251">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="0684c-252">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="0684c-252">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="0684c-253">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="0684c-253">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="0684c-254">No aumente los valores predeterminados de los búferes de proxy a menos que sea necesario.</span><span class="sxs-lookup"><span data-stu-id="0684c-254">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="0684c-255">El aumento de estos valores incrementa el riesgo de saturación del búfer (desbordamiento) y ataques por denegación de servicio (DoS) realizados por usuarios malintencionados.</span><span class="sxs-lookup"><span data-stu-id="0684c-255">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="0684c-256">Protección de la nube</span><span class="sxs-lookup"><span data-stu-id="0684c-256">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="0684c-257">Habilitar AppArmor</span><span class="sxs-lookup"><span data-stu-id="0684c-257">Enable AppArmor</span></span>

<span data-ttu-id="0684c-258">Linux Security Modules (LSM) es una plataforma que forma parte del kernel de Linux desde Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="0684c-258">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="0684c-259">LSM admite diferentes implementaciones de los módulos de seguridad.</span><span class="sxs-lookup"><span data-stu-id="0684c-259">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="0684c-260">[AppArmor](https://wiki.ubuntu.com/AppArmor) es un LSM que implementa un sistema de control de acceso obligatorio que permite restringir el programa a un conjunto limitado de recursos.</span><span class="sxs-lookup"><span data-stu-id="0684c-260">[AppArmor](https://wiki.ubuntu.com/AppArmor) is an LSM that implements a Mandatory Access Control system, which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="0684c-261">Asegúrese de que AppArmor está habilitado y configurado correctamente.</span><span class="sxs-lookup"><span data-stu-id="0684c-261">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="0684c-262">Configuración del firewall</span><span class="sxs-lookup"><span data-stu-id="0684c-262">Configure the firewall</span></span>

<span data-ttu-id="0684c-263">Cierre todos los puertos externos que no se usen.</span><span class="sxs-lookup"><span data-stu-id="0684c-263">Close off all external ports that aren't in use.</span></span> <span data-ttu-id="0684c-264">Uncomplicated Firewall (ufw) proporciona un front-end para `iptables` al proporcionar una CLI para configurar el firewall.</span><span class="sxs-lookup"><span data-stu-id="0684c-264">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="0684c-265">Si tiene algún firewall activado y no está configurado correctamente, este impedirá el acceso a todo el sistema.</span><span class="sxs-lookup"><span data-stu-id="0684c-265">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="0684c-266">Si usa SSH para establecer la conexión y no especifica el puerto SSH correcto, quedará bloqueado y no podrá acceder al sistema.</span><span class="sxs-lookup"><span data-stu-id="0684c-266">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="0684c-267">El puerto predeterminado es el 22.</span><span class="sxs-lookup"><span data-stu-id="0684c-267">The default port is 22.</span></span> <span data-ttu-id="0684c-268">Para obtener más información, consulte la [introducción a UFW](https://help.ubuntu.com/community/UFW) y el [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="0684c-268">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="0684c-269">Instale `ufw` y configúrelo para permitir el tráfico en los puertos que convenga.</span><span class="sxs-lookup"><span data-stu-id="0684c-269">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="0684c-270">Protección de Nginx</span><span class="sxs-lookup"><span data-stu-id="0684c-270">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="0684c-271">Cambiar el nombre de la respuesta de Nginx</span><span class="sxs-lookup"><span data-stu-id="0684c-271">Change the Nginx response name</span></span>

<span data-ttu-id="0684c-272">Edite `src/http/ngx_http_header_filter_module.c`:</span><span class="sxs-lookup"><span data-stu-id="0684c-272">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="0684c-273">Configurar opciones</span><span class="sxs-lookup"><span data-stu-id="0684c-273">Configure options</span></span>

<span data-ttu-id="0684c-274">Configure el servidor con más módulos que sean necesarios.</span><span class="sxs-lookup"><span data-stu-id="0684c-274">Configure the server with additional required modules.</span></span> <span data-ttu-id="0684c-275">Sopese la posibilidad de usar un firewall de aplicación web como [ModSecurity](https://www.modsecurity.org/) para proteger la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-275">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="0684c-276">Configuración de HTTPS</span><span class="sxs-lookup"><span data-stu-id="0684c-276">HTTPS configuration</span></span>

<span data-ttu-id="0684c-277">**Configuración de la aplicación para conexiones locales seguras (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="0684c-277">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="0684c-278">El comando [dotnet run](/dotnet/core/tools/dotnet-run) usa el archivo *Properties/launchSettings.json* de la aplicación, que la configura para que realice escuchas en las direcciones URL proporcionadas por la propiedad `applicationUrl`.</span><span class="sxs-lookup"><span data-stu-id="0684c-278">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property.</span></span> <span data-ttu-id="0684c-279">Por ejemplo, `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="0684c-279">For example, `https://localhost:5001;http://localhost:5000`.</span></span>

<span data-ttu-id="0684c-280">Configure la aplicación para que use un certificado en el desarrollo para el comando `dotnet run` o el entorno de desarrollo (<kbd>F5</kbd> o <kbd>Ctrl</kbd>+<kbd>F5</kbd> en Visual Studio Code) mediante uno de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="0684c-280">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0684c-281">[Reemplace el certificado predeterminado de configuración](xref:fundamentals/servers/kestrel/endpoints#configuration) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="0684c-281">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="0684c-282">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="0684c-282">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0684c-283">[Reemplace el certificado predeterminado de configuración](xref:fundamentals/servers/kestrel#configuration) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="0684c-283">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="0684c-284">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="0684c-284">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="0684c-285">**Configure el proxy inverso para conexiones de cliente seguras (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="0684c-285">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="0684c-286">Configure el servidor para que escuche el tráfico HTTPS en el puerto 443. Para ello, especifique un certificado válido emitido por una entidad de certificados (CA) de confianza.</span><span class="sxs-lookup"><span data-stu-id="0684c-286">Configure the server to listen to HTTPS traffic on port 443 by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="0684c-287">Refuerce la seguridad con algunos de los procedimientos descritos en el siguiente archivo */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="0684c-287">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="0684c-288">Entre los ejemplos se incluye la elección de un cifrado más seguro y el redireccionamiento de todo el tráfico a través de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0684c-288">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="0684c-289">En entornos de desarrollo, se recomienda usar redireccionamientos temporales (302) en lugar de redireccionamientos permanentes (301).</span><span class="sxs-lookup"><span data-stu-id="0684c-289">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="0684c-290">El almacenamiento en caché de vínculos puede producir un comportamiento inestable en los entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="0684c-290">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="0684c-291">Agregar un encabezado `HTTP Strict-Transport-Security` (HSTS) garantiza que todas las solicitudes siguientes realizadas por el cliente son a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0684c-291">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="0684c-292">Para obtener instrucciones importantes sobre HSTS, vea <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span><span class="sxs-lookup"><span data-stu-id="0684c-292">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="0684c-293">Si HTTPS se va a deshabilitar en el futuro, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0684c-293">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="0684c-294">No agregue el encabezado HSTS.</span><span class="sxs-lookup"><span data-stu-id="0684c-294">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="0684c-295">Elija un valor de `max-age` corto.</span><span class="sxs-lookup"><span data-stu-id="0684c-295">Choose a short `max-age` value.</span></span>

<span data-ttu-id="0684c-296">Agregue el archivo de configuración */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="0684c-296">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="0684c-297">**Reemplace** el contenido del archivo de configuración */etc/nginx/nginx.conf* por el archivo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0684c-297">**Replace** the contents of the */etc/nginx/nginx.conf* configuration file with the following file.</span></span> <span data-ttu-id="0684c-298">El ejemplo contiene las dos secciones `http` y `server` en un archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="0684c-298">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="0684c-299">Las aplicaciones de Blazor WebAssembly requieren un valor de parámetro `burst` más grande para acomodar el mayor número de solicitudes hechas por una aplicación.</span><span class="sxs-lookup"><span data-stu-id="0684c-299">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="0684c-300">Para obtener más información, vea <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="0684c-300">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="0684c-301">Proteger Nginx frente al secuestro de clic</span><span class="sxs-lookup"><span data-stu-id="0684c-301">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="0684c-302">El [secuestro de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), también conocido como *redireccionamiento de interfaz de usuario*, es un ataque malintencionado donde al visitante de un sitio web se le engaña para que haga clic en un vínculo o botón de una página distinta de la que actualmente está visitando.</span><span class="sxs-lookup"><span data-stu-id="0684c-302">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="0684c-303">Use `X-FRAME-OPTIONS` para proteger el sitio.</span><span class="sxs-lookup"><span data-stu-id="0684c-303">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="0684c-304">Para mitigar los ataques de secuestro de clic:</span><span class="sxs-lookup"><span data-stu-id="0684c-304">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="0684c-305">Edite el archivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="0684c-305">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="0684c-306">Agregue la línea `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="0684c-306">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="0684c-307">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="0684c-307">Save the file.</span></span>
1. <span data-ttu-id="0684c-308">Reinicie Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-308">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="0684c-309">Examen de tipo MIME</span><span class="sxs-lookup"><span data-stu-id="0684c-309">MIME-type sniffing</span></span>

<span data-ttu-id="0684c-310">Este encabezado evita que la mayoría de los exploradores examinen el MIME de una respuesta fuera del tipo de contenido declarado, ya que el encabezado indica al explorador que no reemplace el tipo de contenido de respuesta.</span><span class="sxs-lookup"><span data-stu-id="0684c-310">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="0684c-311">Con la opción `nosniff`, si el servidor indica que el contenido es `text/html`, el explorador lo representa como `text/html`.</span><span class="sxs-lookup"><span data-stu-id="0684c-311">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="0684c-312">Edite el archivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="0684c-312">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="0684c-313">Agregue la línea `add_header X-Content-Type-Options "nosniff";`.</span><span class="sxs-lookup"><span data-stu-id="0684c-313">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="0684c-314">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="0684c-314">Save the file.</span></span>
1. <span data-ttu-id="0684c-315">Reinicie Nginx.</span><span class="sxs-lookup"><span data-stu-id="0684c-315">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="0684c-316">Sugerencias de Nginx adicionales</span><span class="sxs-lookup"><span data-stu-id="0684c-316">Additional Nginx suggestions</span></span>

<span data-ttu-id="0684c-317">Después de actualizar el marco de trabajo compartido en el servidor, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.</span><span class="sxs-lookup"><span data-stu-id="0684c-317">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0684c-318">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0684c-318">Additional resources</span></span>

* [<span data-ttu-id="0684c-319">Requisitos previos para .NET Core en Linux</span><span class="sxs-lookup"><span data-stu-id="0684c-319">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <span data-ttu-id="0684c-320">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: versiones de código binario: paquetes oficiales de Debian y Ubuntu)</span><span class="sxs-lookup"><span data-stu-id="0684c-320">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)</span></span>
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="0684c-321">[Nginx: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (Nginx: uso del encabezado Forwarded)</span><span class="sxs-lookup"><span data-stu-id="0684c-321">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span></span>
