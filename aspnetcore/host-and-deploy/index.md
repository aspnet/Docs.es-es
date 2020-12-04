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
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556624"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="61e19-103">Hospedaje e implementación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61e19-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="61e19-104">En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="61e19-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="61e19-105">Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="61e19-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="61e19-106">Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="61e19-107">Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61e19-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="61e19-108">Publicar la aplicación en una carpeta</span><span class="sxs-lookup"><span data-stu-id="61e19-108">Publish to a folder</span></span>

<span data-ttu-id="61e19-109">El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="61e19-110">Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.</span><span class="sxs-lookup"><span data-stu-id="61e19-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="61e19-111">Publicación de los archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="61e19-111">Publish settings files</span></span>

<span data-ttu-id="61e19-112">Los archivos `*.json` se publican de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="61e19-112">`*.json` files are published by default.</span></span> <span data-ttu-id="61e19-113">Para publicar otros archivos de configuración, especifíquelos en un elemento [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="61e19-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="61e19-114">En el ejemplo siguiente, se publican archivos XML:</span><span class="sxs-lookup"><span data-stu-id="61e19-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="61e19-115">Contenido de la carpeta</span><span class="sxs-lookup"><span data-stu-id="61e19-115">Folder contents</span></span>

<span data-ttu-id="61e19-116">La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.</span><span class="sxs-lookup"><span data-stu-id="61e19-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="61e19-117">Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="61e19-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="61e19-118">Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="61e19-119">Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="61e19-120">El modelo de implementación predeterminado es dependiente del marco.</span><span class="sxs-lookup"><span data-stu-id="61e19-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="61e19-121">Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="61e19-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="61e19-122">Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="61e19-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="61e19-123">Para obtener más información, vea <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="61e19-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="61e19-124">Configurar un administrador de procesos</span><span class="sxs-lookup"><span data-stu-id="61e19-124">Set up a process manager</span></span>

<span data-ttu-id="61e19-125">Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea.</span><span class="sxs-lookup"><span data-stu-id="61e19-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="61e19-126">Para automatizar los inicios y los reinicios, se necesita un administrador de procesos.</span><span class="sxs-lookup"><span data-stu-id="61e19-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="61e19-127">Los administradores de procesos más comunes para ASP.NET Core son:</span><span class="sxs-lookup"><span data-stu-id="61e19-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="61e19-128">Linux</span><span class="sxs-lookup"><span data-stu-id="61e19-128">Linux</span></span>
  * [<span data-ttu-id="61e19-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="61e19-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="61e19-130">Apache</span><span class="sxs-lookup"><span data-stu-id="61e19-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="61e19-131">Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-131">Windows</span></span>
  * [<span data-ttu-id="61e19-132">IIS</span><span class="sxs-lookup"><span data-stu-id="61e19-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="61e19-133">Servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="61e19-134">Configurar un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="61e19-134">Set up a reverse proxy</span></span>

<span data-ttu-id="61e19-135">Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="61e19-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="61e19-136">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="61e19-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="61e19-137">Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="61e19-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="61e19-138">Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="61e19-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="61e19-139">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="61e19-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="61e19-140">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga.</span><span class="sxs-lookup"><span data-stu-id="61e19-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="61e19-141">Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud.</span><span class="sxs-lookup"><span data-stu-id="61e19-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="61e19-142">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="61e19-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="61e19-143">Uso de Visual Studio y MSBuild para automatizar implementaciones</span><span class="sxs-lookup"><span data-stu-id="61e19-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="61e19-144">La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="61e19-145">Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="61e19-146">Para la implementación web, Visual Studio usa [MSBuild](/visualstudio/msbuild/msbuild), que se puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="61e19-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="61e19-147">Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="61e19-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="61e19-148">Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="61e19-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="61e19-149">Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="61e19-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="61e19-150">Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="61e19-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="61e19-151">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="61e19-151">Publish to Azure</span></span>

<span data-ttu-id="61e19-152">Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61e19-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="61e19-153">[Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.</span><span class="sxs-lookup"><span data-stu-id="61e19-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="61e19-154">Publicar con MSDeploy en Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="61e19-155">Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="61e19-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="61e19-156">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="61e19-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="61e19-157">Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="61e19-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="61e19-158">Hospedaje en una granja de servidores web</span><span class="sxs-lookup"><span data-stu-id="61e19-158">Host in a web farm</span></span>

<span data-ttu-id="61e19-159">Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="61e19-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="61e19-160">Host en Docker</span><span class="sxs-lookup"><span data-stu-id="61e19-160">Host on Docker</span></span>

<span data-ttu-id="61e19-161">Para obtener más información, vea <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="61e19-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="61e19-162">Realización de comprobaciones de estado</span><span class="sxs-lookup"><span data-stu-id="61e19-162">Perform health checks</span></span>

<span data-ttu-id="61e19-163">Use middleware de comprobación de estado para realizar comprobaciones de estado en una aplicación y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="61e19-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="61e19-164">Para obtener más información, vea <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="61e19-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61e19-165">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="61e19-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="61e19-166">Hospedaje de ASP.NET</span><span class="sxs-lookup"><span data-stu-id="61e19-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="61e19-167">En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="61e19-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="61e19-168">Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="61e19-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="61e19-169">Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="61e19-170">Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61e19-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="61e19-171">Publicar la aplicación en una carpeta</span><span class="sxs-lookup"><span data-stu-id="61e19-171">Publish to a folder</span></span>

<span data-ttu-id="61e19-172">El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="61e19-173">Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.</span><span class="sxs-lookup"><span data-stu-id="61e19-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="61e19-174">Contenido de la carpeta</span><span class="sxs-lookup"><span data-stu-id="61e19-174">Folder contents</span></span>

<span data-ttu-id="61e19-175">La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.</span><span class="sxs-lookup"><span data-stu-id="61e19-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="61e19-176">Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="61e19-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="61e19-177">Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="61e19-178">Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="61e19-179">El modelo de implementación predeterminado es dependiente del marco.</span><span class="sxs-lookup"><span data-stu-id="61e19-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="61e19-180">Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="61e19-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="61e19-181">Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="61e19-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="61e19-182">Para obtener más información, vea <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="61e19-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="61e19-183">Configurar un administrador de procesos</span><span class="sxs-lookup"><span data-stu-id="61e19-183">Set up a process manager</span></span>

<span data-ttu-id="61e19-184">Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea.</span><span class="sxs-lookup"><span data-stu-id="61e19-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="61e19-185">Para automatizar los inicios y los reinicios, se necesita un administrador de procesos.</span><span class="sxs-lookup"><span data-stu-id="61e19-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="61e19-186">Los administradores de procesos más comunes para ASP.NET Core son:</span><span class="sxs-lookup"><span data-stu-id="61e19-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="61e19-187">Linux</span><span class="sxs-lookup"><span data-stu-id="61e19-187">Linux</span></span>
  * [<span data-ttu-id="61e19-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="61e19-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="61e19-189">Apache</span><span class="sxs-lookup"><span data-stu-id="61e19-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="61e19-190">Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-190">Windows</span></span>
  * [<span data-ttu-id="61e19-191">IIS</span><span class="sxs-lookup"><span data-stu-id="61e19-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="61e19-192">Servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="61e19-193">Configurar un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="61e19-193">Set up a reverse proxy</span></span>

<span data-ttu-id="61e19-194">Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="61e19-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="61e19-195">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="61e19-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="61e19-196">Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida.</span><span class="sxs-lookup"><span data-stu-id="61e19-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="61e19-197">Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="61e19-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="61e19-198">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="61e19-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="61e19-199">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga.</span><span class="sxs-lookup"><span data-stu-id="61e19-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="61e19-200">Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud.</span><span class="sxs-lookup"><span data-stu-id="61e19-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="61e19-201">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="61e19-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="61e19-202">Uso de Visual Studio y MSBuild para automatizar implementaciones</span><span class="sxs-lookup"><span data-stu-id="61e19-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="61e19-203">La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor.</span><span class="sxs-lookup"><span data-stu-id="61e19-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="61e19-204">Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*.</span><span class="sxs-lookup"><span data-stu-id="61e19-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="61e19-205">Para la implementación web, Visual Studio usa MSBuild, que puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="61e19-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="61e19-206">Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="61e19-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="61e19-207">Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="61e19-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="61e19-208">Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="61e19-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="61e19-209">Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="61e19-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="61e19-210">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="61e19-210">Publish to Azure</span></span>

<span data-ttu-id="61e19-211">Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61e19-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="61e19-212">[Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.</span><span class="sxs-lookup"><span data-stu-id="61e19-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="61e19-213">Publicar con MSDeploy en Windows</span><span class="sxs-lookup"><span data-stu-id="61e19-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="61e19-214">Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="61e19-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="61e19-215">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="61e19-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="61e19-216">Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="61e19-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="61e19-217">Hospedaje en una granja de servidores web</span><span class="sxs-lookup"><span data-stu-id="61e19-217">Host in a web farm</span></span>

<span data-ttu-id="61e19-218">Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="61e19-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="61e19-219">Host en Docker</span><span class="sxs-lookup"><span data-stu-id="61e19-219">Host on Docker</span></span>

<span data-ttu-id="61e19-220">Para obtener más información, vea <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="61e19-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61e19-221">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="61e19-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="61e19-222">Hospedaje de ASP.NET</span><span class="sxs-lookup"><span data-stu-id="61e19-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
