---
title: Hospedaje e implementación de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar entornos de hospedaje e implementar aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252973"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="30660-103">Hospedaje e implementación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30660-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="30660-104">En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="30660-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="30660-105">Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="30660-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="30660-106">Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="30660-107">Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30660-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="30660-108">Publicar la aplicación en una carpeta</span><span class="sxs-lookup"><span data-stu-id="30660-108">Publish to a folder</span></span>

<span data-ttu-id="30660-109">El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="30660-110">Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.</span><span class="sxs-lookup"><span data-stu-id="30660-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="30660-111">Publicación de los archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="30660-111">Publish settings files</span></span>

<span data-ttu-id="30660-112">Los archivos `*.json` se publican de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="30660-112">`*.json` files are published by default.</span></span> <span data-ttu-id="30660-113">Para publicar otros archivos de configuración, especifíquelos en un elemento [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="30660-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="30660-114">En el ejemplo siguiente, se publican archivos XML:</span><span class="sxs-lookup"><span data-stu-id="30660-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="30660-115">Contenido de la carpeta</span><span class="sxs-lookup"><span data-stu-id="30660-115">Folder contents</span></span>

<span data-ttu-id="30660-116">La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.</span><span class="sxs-lookup"><span data-stu-id="30660-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="30660-117">Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="30660-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="30660-118">Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="30660-119">Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="30660-120">El modelo de implementación predeterminado es dependiente del marco.</span><span class="sxs-lookup"><span data-stu-id="30660-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="30660-121">Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="30660-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="30660-122">Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="30660-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="30660-123">Para obtener más información, vea <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="30660-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="30660-124">Configurar un administrador de procesos</span><span class="sxs-lookup"><span data-stu-id="30660-124">Set up a process manager</span></span>

<span data-ttu-id="30660-125">Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea.</span><span class="sxs-lookup"><span data-stu-id="30660-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="30660-126">Para automatizar los inicios y los reinicios, se necesita un administrador de procesos.</span><span class="sxs-lookup"><span data-stu-id="30660-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="30660-127">Los administradores de procesos más comunes para ASP.NET Core son:</span><span class="sxs-lookup"><span data-stu-id="30660-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="30660-128">Linux</span><span class="sxs-lookup"><span data-stu-id="30660-128">Linux</span></span>
  * [<span data-ttu-id="30660-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="30660-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="30660-130">Apache</span><span class="sxs-lookup"><span data-stu-id="30660-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="30660-131">Windows</span><span class="sxs-lookup"><span data-stu-id="30660-131">Windows</span></span>
  * [<span data-ttu-id="30660-132">IIS</span><span class="sxs-lookup"><span data-stu-id="30660-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="30660-133">Servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="30660-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="30660-134">Configurar un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="30660-134">Set up a reverse proxy</span></span>

<span data-ttu-id="30660-135">Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="30660-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="30660-136">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="30660-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="30660-137">Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="30660-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="30660-138">Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="30660-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="30660-139">Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="30660-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="30660-140">Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="30660-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="30660-141">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="30660-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="30660-142">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga.</span><span class="sxs-lookup"><span data-stu-id="30660-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="30660-143">Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud.</span><span class="sxs-lookup"><span data-stu-id="30660-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="30660-144">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="30660-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="30660-145">Uso de Visual Studio y MSBuild para automatizar implementaciones</span><span class="sxs-lookup"><span data-stu-id="30660-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="30660-146">La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="30660-147">Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="30660-148">Para la implementación web, Visual Studio usa [MSBuild](/visualstudio/msbuild/msbuild), que se puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="30660-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="30660-149">Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="30660-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="30660-150">Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="30660-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="30660-151">Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="30660-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="30660-152">Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="30660-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="30660-153">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="30660-153">Publish to Azure</span></span>

<span data-ttu-id="30660-154">Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="30660-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="30660-155">[Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.</span><span class="sxs-lookup"><span data-stu-id="30660-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="30660-156">Publicar con MSDeploy en Windows</span><span class="sxs-lookup"><span data-stu-id="30660-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="30660-157">Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="30660-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="30660-158">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="30660-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="30660-159">Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="30660-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="30660-160">Hospedaje en una granja de servidores web</span><span class="sxs-lookup"><span data-stu-id="30660-160">Host in a web farm</span></span>

<span data-ttu-id="30660-161">Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="30660-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="30660-162">Host en Docker</span><span class="sxs-lookup"><span data-stu-id="30660-162">Host on Docker</span></span>

<span data-ttu-id="30660-163">Para obtener más información, vea <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="30660-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="30660-164">Realización de comprobaciones de estado</span><span class="sxs-lookup"><span data-stu-id="30660-164">Perform health checks</span></span>

<span data-ttu-id="30660-165">Use middleware de comprobación de estado para realizar comprobaciones de estado en una aplicación y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="30660-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="30660-166">Para obtener más información, vea <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="30660-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30660-167">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="30660-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="30660-168">Hospedaje de ASP.NET</span><span class="sxs-lookup"><span data-stu-id="30660-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="30660-169">En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="30660-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="30660-170">Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="30660-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="30660-171">Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="30660-172">Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30660-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="30660-173">Publicar la aplicación en una carpeta</span><span class="sxs-lookup"><span data-stu-id="30660-173">Publish to a folder</span></span>

<span data-ttu-id="30660-174">El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="30660-175">Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.</span><span class="sxs-lookup"><span data-stu-id="30660-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="30660-176">Contenido de la carpeta</span><span class="sxs-lookup"><span data-stu-id="30660-176">Folder contents</span></span>

<span data-ttu-id="30660-177">La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.</span><span class="sxs-lookup"><span data-stu-id="30660-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="30660-178">Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="30660-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="30660-179">Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="30660-180">Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="30660-181">El modelo de implementación predeterminado es dependiente del marco.</span><span class="sxs-lookup"><span data-stu-id="30660-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="30660-182">Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="30660-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="30660-183">Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="30660-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="30660-184">Para obtener más información, vea <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="30660-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="30660-185">Configurar un administrador de procesos</span><span class="sxs-lookup"><span data-stu-id="30660-185">Set up a process manager</span></span>

<span data-ttu-id="30660-186">Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea.</span><span class="sxs-lookup"><span data-stu-id="30660-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="30660-187">Para automatizar los inicios y los reinicios, se necesita un administrador de procesos.</span><span class="sxs-lookup"><span data-stu-id="30660-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="30660-188">Los administradores de procesos más comunes para ASP.NET Core son:</span><span class="sxs-lookup"><span data-stu-id="30660-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="30660-189">Linux</span><span class="sxs-lookup"><span data-stu-id="30660-189">Linux</span></span>
  * [<span data-ttu-id="30660-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="30660-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="30660-191">Apache</span><span class="sxs-lookup"><span data-stu-id="30660-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="30660-192">Windows</span><span class="sxs-lookup"><span data-stu-id="30660-192">Windows</span></span>
  * [<span data-ttu-id="30660-193">IIS</span><span class="sxs-lookup"><span data-stu-id="30660-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="30660-194">Servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="30660-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="30660-195">Configurar un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="30660-195">Set up a reverse proxy</span></span>

<span data-ttu-id="30660-196">Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="30660-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="30660-197">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="30660-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="30660-198">Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="30660-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="30660-199">Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="30660-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="30660-200">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="30660-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="30660-201">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga.</span><span class="sxs-lookup"><span data-stu-id="30660-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="30660-202">Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud.</span><span class="sxs-lookup"><span data-stu-id="30660-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="30660-203">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="30660-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="30660-204">Uso de Visual Studio y MSBuild para automatizar implementaciones</span><span class="sxs-lookup"><span data-stu-id="30660-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="30660-205">La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor.</span><span class="sxs-lookup"><span data-stu-id="30660-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="30660-206">Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="30660-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="30660-207">Para la implementación web, Visual Studio usa MSBuild, que puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="30660-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="30660-208">Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="30660-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="30660-209">Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="30660-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="30660-210">Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="30660-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="30660-211">Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="30660-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="30660-212">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="30660-212">Publish to Azure</span></span>

<span data-ttu-id="30660-213">Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="30660-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="30660-214">[Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.</span><span class="sxs-lookup"><span data-stu-id="30660-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="30660-215">Publicar con MSDeploy en Windows</span><span class="sxs-lookup"><span data-stu-id="30660-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="30660-216">Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="30660-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="30660-217">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="30660-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="30660-218">Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="30660-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="30660-219">Hospedaje en una granja de servidores web</span><span class="sxs-lookup"><span data-stu-id="30660-219">Host in a web farm</span></span>

<span data-ttu-id="30660-220">Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="30660-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="30660-221">Host en Docker</span><span class="sxs-lookup"><span data-stu-id="30660-221">Host on Docker</span></span>

<span data-ttu-id="30660-222">Para obtener más información, vea <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="30660-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30660-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="30660-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="30660-224">Hospedaje de ASP.NET</span><span class="sxs-lookup"><span data-stu-id="30660-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
