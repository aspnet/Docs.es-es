---
title: Implementar aplicaciones de ASP.NET Core en Azure App Service
author: bradygaster
description: Este artículo contiene vínculos a recursos de implementación y hospedaje de Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
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
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 11de6b04f6813161e5eaee294f3e67e223ae0db3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015925"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="46df8-103">Implementar aplicaciones de ASP.NET Core en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="46df8-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="46df8-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) es un [servicio de plataforma de informática en la nube de Microsoft](https://azure.microsoft.com/) que sirve para hospedar aplicaciones web, como ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="46df8-105">Recursos útiles</span><span class="sxs-lookup"><span data-stu-id="46df8-105">Useful resources</span></span>

<span data-ttu-id="46df8-106">La [documentación de App Service](/azure/app-service/) es un recurso que incluye documentación, tutoriales, ejemplos, guías de procedimientos y otros recursos de aplicaciones de Azure.</span><span class="sxs-lookup"><span data-stu-id="46df8-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="46df8-107">Dos tutoriales importantes que pertenecen al hospedaje de aplicaciones de ASP.NET Core son:</span><span class="sxs-lookup"><span data-stu-id="46df8-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="46df8-108">Creación de una aplicación web ASP.NET Core en Azure</span><span class="sxs-lookup"><span data-stu-id="46df8-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="46df8-109">Usar Visual Studio para crear e implementar una aplicación web de ASP.NET Core para Azure App Service en Windows.</span><span class="sxs-lookup"><span data-stu-id="46df8-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="46df8-110">Creación de una aplicación ASP.NET Core en App Service en Linux</span><span class="sxs-lookup"><span data-stu-id="46df8-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="46df8-111">Usar la línea de comandos para crear e implementar una aplicación web de ASP.NET Core para Azure App Service en Linux.</span><span class="sxs-lookup"><span data-stu-id="46df8-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="46df8-112">Consulte el [panel ASP.NET Core en App Service](https://aspnetcoreon.azurewebsites.net/) para ver la versión de ASP.NET Core disponible en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="46df8-113">Suscríbase al repositorio [App Service Announcements](https://github.com/Azure/app-service-announcements/) y supervise los problemas.</span><span class="sxs-lookup"><span data-stu-id="46df8-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="46df8-114">El equipo de App Service publica periódicamente los anuncios y escenarios que llegan a App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="46df8-115">Los artículos siguientes están disponibles en la documentación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="46df8-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="46df8-116">Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="46df8-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="46df8-117">Obtenga información sobre cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla en Azure App Service con Git para una implementación continua.</span><span class="sxs-lookup"><span data-stu-id="46df8-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="46df8-118">Creación de la primera canalización</span><span class="sxs-lookup"><span data-stu-id="46df8-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="46df8-119">Configure una compilación de integración continua para una aplicación de ASP.NET Core y, después, cree una versión de implementación continua para Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="46df8-120">Espacio aislado de Azure Web App</span><span class="sxs-lookup"><span data-stu-id="46df8-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="46df8-121">Detecte limitaciones de ejecución en tiempo de ejecución de Azure App Service aplicadas por la plataforma de aplicaciones de Azure.</span><span class="sxs-lookup"><span data-stu-id="46df8-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="46df8-122">Conozca y solucione advertencias y errores en proyectos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="46df8-123">Configuración de aplicación</span><span class="sxs-lookup"><span data-stu-id="46df8-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="46df8-124">Plataforma</span><span class="sxs-lookup"><span data-stu-id="46df8-124">Platform</span></span>

<span data-ttu-id="46df8-125">La arquitectura de plataforma (x86/x64) de una aplicación de App Services se establece en la configuración de la aplicación de Azure Portal para las aplicaciones hospedadas en un cálculo de serie A (básico) o en un nivel de hospedaje superior.</span><span class="sxs-lookup"><span data-stu-id="46df8-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="46df8-126">Confirme que la configuración de publicación de la aplicación (por ejemplo, en el [perfil de publicación [.pubxml])](xref:host-and-deploy/visual-studio-publish-profiles) de Visual Studio coincide con los valores de configuración de servicio de la aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="46df8-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="46df8-127">Los runtimes para aplicaciones de 64 bits (x64) y 32 bits (x86) están presentes en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="46df8-128">El [SDK de .NET Core](/dotnet/core/sdk) que está disponible en App Service es para 32 bits, pero es posible implementar aplicaciones de 64 bits compiladas localmente con la consola de [Kudu](https://github.com/projectkudu/kudu/wiki) o el proceso de publicación de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="46df8-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="46df8-129">Para obtener más información, consulte la sección [Publicar e implementar la aplicación](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="46df8-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="46df8-130">En el caso de las aplicaciones con dependencias nativas, los runtimes de 32 bits (x86) están presentes Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="46df8-131">El [SDK de .NET Core](/dotnet/core/sdk) disponible en App Service es de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="46df8-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="46df8-132">Para obtener más detalles sobre los componentes y los métodos de distribución del marco .NET Core —por ejemplo, información sobre .NET Core Runtime—, consulte [Sobre .NET Core: Composición](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="46df8-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="46df8-133">Paquetes</span><span class="sxs-lookup"><span data-stu-id="46df8-133">Packages</span></span>

<span data-ttu-id="46df8-134">Incluya los siguientes paquetes de NuGet para proporcionar características de registro automáticas para aplicaciones implementadas en Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="46df8-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="46df8-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para proporcionar a ASP.NET Core una integración ligera (Light-Up) con Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="46df8-136">El paquete `Microsoft.AspNetCore.AzureAppServicesIntegration` proporciona las características de registro agregadas.</span><span class="sxs-lookup"><span data-stu-id="46df8-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="46df8-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) ejecuta [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para agregar a Azure App Service proveedores de registro de diagnósticos en el paquete `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="46df8-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="46df8-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) proporciona implementaciones de registrador para admitir registros de diagnósticos de Azure App Service y características de transmisión en secuencias de registro.</span><span class="sxs-lookup"><span data-stu-id="46df8-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="46df8-139">Los paquetes anteriores no están disponibles en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="46df8-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="46df8-140">Las aplicaciones que tienen como destino .NET Framework o hacen referencia al metapaquete `Microsoft.AspNetCore.App` deben hacer referencia de forma explícita a los paquetes individuales del archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="46df8-141">Invalidación de la configuración de la aplicación mediante Azure Portal</span><span class="sxs-lookup"><span data-stu-id="46df8-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46df8-142">La configuración de la aplicación en Azure Portal le permite establecer variables de entorno para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="46df8-143">El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables) puede consumir las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="46df8-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="46df8-144">Cuando una configuración de aplicación se crea o modifica en Azure Portal y el botón **Guardar** está seleccionado, se reinicia la aplicación de Azure.</span><span class="sxs-lookup"><span data-stu-id="46df8-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="46df8-145">La variable de entorno está disponible para la aplicación después de que se reinicie el servicio.</span><span class="sxs-lookup"><span data-stu-id="46df8-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="46df8-146">Cuando una aplicación usa el [host genérico](xref:fundamentals/host/generic-host), las variables de entorno se cargan en la configuración de la aplicación cuando se llama a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> para compilar el host.</span><span class="sxs-lookup"><span data-stu-id="46df8-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="46df8-147">Para más información, consulte <xref:fundamentals/host/generic-host> y el [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="46df8-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46df8-148">La configuración de la aplicación en Azure Portal le permite establecer variables de entorno para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="46df8-149">El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) puede consumir las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="46df8-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="46df8-150">Cuando una configuración de aplicación se crea o modifica en Azure Portal y el botón **Guardar** está seleccionado, se reinicia la aplicación de Azure.</span><span class="sxs-lookup"><span data-stu-id="46df8-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="46df8-151">La variable de entorno está disponible para la aplicación después de que se reinicie el servicio.</span><span class="sxs-lookup"><span data-stu-id="46df8-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="46df8-152">Cuando una aplicación usa el [host web](xref:fundamentals/host/web-host), las variables de entorno se cargan en la configuración de la aplicación cuando se llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para compilar el host.</span><span class="sxs-lookup"><span data-stu-id="46df8-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="46df8-153">Para más información, consulte <xref:fundamentals/host/web-host> y el [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="46df8-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="46df8-154">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="46df8-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="46df8-155">El [middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), que configura el software intermedio de encabezados reenviados al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), y el módulo de ASP.NET Core están configurados para reenviar el esquema (HTTP/HTTPS) y la dirección IP remota donde se originó la solicitud.</span><span class="sxs-lookup"><span data-stu-id="46df8-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="46df8-156">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales.</span><span class="sxs-lookup"><span data-stu-id="46df8-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="46df8-157">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="46df8-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="46df8-158">Supervisión y registro</span><span class="sxs-lookup"><span data-stu-id="46df8-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46df8-159">Las aplicaciones ASP.NET Core implementadas de forma automática en App Service reciben una extensión de App Service, **Integración de registro de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="46df8-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="46df8-160">La extensión habilita la integración de registro para las aplicaciones ASP.NET Core en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46df8-161">Las aplicaciones de ASP.NET Core implementadas automáticamente en App Service reciben una extensión de App Service, **Extensiones de registro de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="46df8-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="46df8-162">La extensión habilita la integración de registro para las aplicaciones ASP.NET Core en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="46df8-163">Para obtener información sobre supervisión, registro y solución de problemas, consulte los artículos siguientes:</span><span class="sxs-lookup"><span data-stu-id="46df8-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="46df8-164">Supervisar aplicaciones en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="46df8-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="46df8-165">Obtenga información sobre cómo revisar las cuotas y las métricas para las aplicaciones y los planes de App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="46df8-166">Habilitar el registro de diagnósticos para las aplicaciones en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="46df8-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="46df8-167">Descubra cómo habilitar y acceder a registro de diagnóstico para los códigos de estado HTTP, solicitudes con error y actividad del servidor web.</span><span class="sxs-lookup"><span data-stu-id="46df8-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="46df8-168">Conozca los métodos habituales para controlar los errores en las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="46df8-169">Obtenga información sobre cómo diagnosticar problemas con las implementaciones de Azure App Service con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="46df8-170">Consulte los errores comunes de configuración de implementación para las aplicaciones hospedadas por Azure App Service/IIS con consejos de solución de problemas.</span><span class="sxs-lookup"><span data-stu-id="46df8-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="46df8-171">Anillo de clave de protección de datos y ranuras de implementación</span><span class="sxs-lookup"><span data-stu-id="46df8-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="46df8-172">Las [claves de protección de datos](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) se conservan en la carpeta *%HOME%\ASP.NET\DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="46df8-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="46df8-173">Esta carpeta está respaldada por el almacenamiento de red y se sincroniza en todas las máquinas que hospedan la aplicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="46df8-174">Las claves no están protegidas en reposo.</span><span class="sxs-lookup"><span data-stu-id="46df8-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="46df8-175">Esta carpeta proporciona el anillo de clave a todas las instancias de una aplicación en una única ranura de implementación.</span><span class="sxs-lookup"><span data-stu-id="46df8-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="46df8-176">Las ranuras de implementación independientes, por ejemplo, almacenamiento provisional y producción, no comparten ningún anillo de clave.</span><span class="sxs-lookup"><span data-stu-id="46df8-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="46df8-177">Al realizar un intercambio entre ranuras de implementación, cualquier sistema que utilice la protección de datos no podrá descifrar los datos almacenados mediante el anillo de clave situado dentro de la ranura anterior.</span><span class="sxs-lookup"><span data-stu-id="46df8-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="46df8-178">El middleware de Cookie de ASP.NET usa protección de datos para proteger sus cookies.</span><span class="sxs-lookup"><span data-stu-id="46df8-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="46df8-179">Esto hace que los usuarios cierren sesión en una aplicación que usa el middleware de Cookie de ASP.NET estándar.</span><span class="sxs-lookup"><span data-stu-id="46df8-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="46df8-180">Para una solución de anillo de clave independiente de la ranura, utilice un proveedor de anillo de clave externo, como estos:</span><span class="sxs-lookup"><span data-stu-id="46df8-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="46df8-181">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="46df8-181">Azure Blob Storage</span></span>
* <span data-ttu-id="46df8-182">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="46df8-182">Azure Key Vault</span></span>
* <span data-ttu-id="46df8-183">Almacén SQL</span><span class="sxs-lookup"><span data-stu-id="46df8-183">SQL store</span></span>
* <span data-ttu-id="46df8-184">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="46df8-184">Redis cache</span></span>

<span data-ttu-id="46df8-185">Para obtener más información, vea <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="46df8-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="46df8-186">Implementación de una aplicación ASP.NET Core que usa una versión preliminar de .NET Core</span><span class="sxs-lookup"><span data-stu-id="46df8-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="46df8-187">Para implementar una aplicación que usa una versión preliminar de .NET Core, consulte los siguientes recursos.</span><span class="sxs-lookup"><span data-stu-id="46df8-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="46df8-188">Estos enfoques también se usan cuando el entorno de ejecución está disponible pero el SDK no se ha instalado en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="46df8-189">Especificación del SDK de .NET Core con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="46df8-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="46df8-190">Implementación de la versión preliminar de una aplicación independiente</span><span class="sxs-lookup"><span data-stu-id="46df8-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="46df8-191">Usar Docker con Web Apps para contenedores</span><span class="sxs-lookup"><span data-stu-id="46df8-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="46df8-192">Instalación de la extensión de sitio de versión preliminar</span><span class="sxs-lookup"><span data-stu-id="46df8-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="46df8-193">Consulte el [panel ASP.NET Core en App Service](https://aspnetcoreon.azurewebsites.net/) para ver la versión de ASP.NET Core disponible en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="46df8-194">Especificación de la versión del SDK de .NET Core con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="46df8-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="46df8-195">Use los [escenarios de CI/CD de Azure App Service](/azure/app-service/deploy-continuous-deployment) para configurar una compilación de integración continua con Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="46df8-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="46df8-196">Después de crear la compilación de Azure DevOps, tiene la opción de configurar la compilación para usar una versión específica del SDK.</span><span class="sxs-lookup"><span data-stu-id="46df8-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="46df8-197">Especificación de la versión del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="46df8-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="46df8-198">Al usar el centro de implementación de App Service para crear una compilación de Azure DevOps, la canalización de compilación predeterminada incluye los pasos `Restore`, `Build`, `Test` y `Publish`.</span><span class="sxs-lookup"><span data-stu-id="46df8-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="46df8-199">Para especificar la versión del SDK, seleccione el botón **Add (+)** (Agregar [+]) en la lista de trabajos del agente para agregar un nuevo paso.</span><span class="sxs-lookup"><span data-stu-id="46df8-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="46df8-200">Busque **.NET Core SDK** (SDK de .NET Core) en la barra de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="46df8-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Paso para agregar el SDK de .NET Core](index/add-sdk-step.png)

<span data-ttu-id="46df8-202">Mueva el paso a la primera posición de la compilación para que los pasos siguientes usen la versión especificada del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="46df8-203">Especifique la versión del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="46df8-204">En este ejemplo, el SDK se establece en `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="46df8-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Paso del SDK completado](index/sdk-step-first-place.png)

<span data-ttu-id="46df8-206">Para publicar una [implementación independiente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configúrela en el paso `Publish` y proporcione el [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="46df8-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publicación independiente](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="46df8-208">Implementación de la versión preliminar de una aplicación independiente</span><span class="sxs-lookup"><span data-stu-id="46df8-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="46df8-209">Una [implementación independiente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) que tiene como destino un entorno de ejecución en versión preliminar transporta el entorno de ejecución en versión preliminar en la implementación.</span><span class="sxs-lookup"><span data-stu-id="46df8-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="46df8-210">Al implementar una aplicación independiente:</span><span class="sxs-lookup"><span data-stu-id="46df8-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="46df8-211">El sitio en Azure App Service no requiere la [extensión del sitio en versión preliminar](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="46df8-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="46df8-212">Se debe publicar la aplicación siguiendo un enfoque diferente que cuando se publica para una [implementación dependiente del marco (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="46df8-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="46df8-213">Siga las instrucciones de la sección [Implementación de la aplicación independiente](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="46df8-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="46df8-214">Usar Docker con Web Apps para contenedores</span><span class="sxs-lookup"><span data-stu-id="46df8-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="46df8-215">[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contiene las imágenes de Docker de versión preliminar más recientes.</span><span class="sxs-lookup"><span data-stu-id="46df8-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="46df8-216">Las imágenes se pueden usar como base.</span><span class="sxs-lookup"><span data-stu-id="46df8-216">The images can be used as a base image.</span></span> <span data-ttu-id="46df8-217">Use la imagen y efectúe la implementación en Web App for Containers con normalidad.</span><span class="sxs-lookup"><span data-stu-id="46df8-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="46df8-218">Instalación de la extensión de sitio de versión preliminar</span><span class="sxs-lookup"><span data-stu-id="46df8-218">Install the preview site extension</span></span>

<span data-ttu-id="46df8-219">Si tiene algún problema al usar la extensión de sitio de versión preliminar, abra una [incidencia de dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="46df8-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="46df8-220">En Azure Portal, vaya a App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="46df8-221">Seleccione la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="46df8-221">Select the web app.</span></span>
1. <span data-ttu-id="46df8-222">Escriba "ex" en el cuadro de búsqueda para filtrar por "Extensiones" o desplácese hacia abajo en la lista de herramientas de administración.</span><span class="sxs-lookup"><span data-stu-id="46df8-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="46df8-223">Seleccione **Extensiones**.</span><span class="sxs-lookup"><span data-stu-id="46df8-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="46df8-224">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-224">Select **Add**.</span></span>
1. <span data-ttu-id="46df8-225">Seleccione la extensión **Runtime de ASP.NET Core {X.Y} ({x64|x86})** en la lista, en la que `{X.Y}` es la versión preliminar de ASP.NET Core y `{x64|x86}` especifica la plataforma.</span><span class="sxs-lookup"><span data-stu-id="46df8-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="46df8-226">Seleccione **Aceptar** para aceptar los términos legales.</span><span class="sxs-lookup"><span data-stu-id="46df8-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="46df8-227">Seleccione **Aceptar** para instalar la extensión.</span><span class="sxs-lookup"><span data-stu-id="46df8-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="46df8-228">Cuando se complete la operación, se instalará la versión preliminar de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="46df8-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="46df8-229">Compruebe la instalación:</span><span class="sxs-lookup"><span data-stu-id="46df8-229">Verify the installation:</span></span>

1. <span data-ttu-id="46df8-230">Seleccione **Herramientas avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="46df8-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="46df8-231">Seleccione **Ir** en **Herramientas avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="46df8-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="46df8-232">Seleccione el elemento de menú **Consola de depuración** > **PowerShell**.</span><span class="sxs-lookup"><span data-stu-id="46df8-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="46df8-233">Ejecute el siguiente comando en el símbolo del sistema de PowerShell:</span><span class="sxs-lookup"><span data-stu-id="46df8-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="46df8-234">Sustituya la versión de runtime de ASP.NET Core por `{X.Y}` y la plataforma por `{PLATFORM}` en el comando:</span><span class="sxs-lookup"><span data-stu-id="46df8-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="46df8-235">El comando devuelve `True` cuando está instalado el runtime de la versión preliminar de x64.</span><span class="sxs-lookup"><span data-stu-id="46df8-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="46df8-236">La arquitectura de plataforma (x86/x64) de una aplicación de App Services se establece en la configuración de la aplicación de Azure Portal para las aplicaciones hospedadas en un cálculo de serie A (básico) o en un nivel de hospedaje superior.</span><span class="sxs-lookup"><span data-stu-id="46df8-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="46df8-237">Confirme que la configuración de publicación de la aplicación (por ejemplo, en el [perfil de publicación [.pubxml])](xref:host-and-deploy/visual-studio-publish-profiles) de Visual Studio coincide con los valores de configuración de servicio de la aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="46df8-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="46df8-238">Si la aplicación se ejecuta en modo de en proceso y la arquitectura de plataforma está configurada para 64 bits (x64), el módulo ASP.NET Core usa el runtime de la versión preliminar de 64 bits, si está presente.</span><span class="sxs-lookup"><span data-stu-id="46df8-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="46df8-239">Instale la extensión del **entorno de ejecución de ASP.NET Core {X.Y} (x64)** mediante Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="46df8-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="46df8-240">Después de instalar el entorno de ejecución de la versión preliminar de x64, ejecute el siguiente comando en la ventana de comandos de Kudu PowerShell de Azure para comprobar la instalación.</span><span class="sxs-lookup"><span data-stu-id="46df8-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="46df8-241">Sustituya la versión del entorno de ejecución de ASP.NET Core por `{X.Y}` en el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="46df8-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="46df8-242">El comando devuelve `True` cuando está instalado el runtime de la versión preliminar de x64.</span><span class="sxs-lookup"><span data-stu-id="46df8-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="46df8-243">Con **Extensiones de ASP.NET Core** se obtienen funciones adicionales para ASP.NET Core en Azure App Services, como habilitar el registro de Azure.</span><span class="sxs-lookup"><span data-stu-id="46df8-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="46df8-244">La extensión se instala automáticamente cuando se implementa desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="46df8-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="46df8-245">Si no se instala la extensión, instálela para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="46df8-246">**Uso de la extensión de sitio de versión preliminar con una plantilla de ARM**</span><span class="sxs-lookup"><span data-stu-id="46df8-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="46df8-247">Si usa una plantilla de ARM para crear e implementar aplicaciones, puede usar el tipo de recurso `siteextensions` para agregar la extensión de sitio a una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="46df8-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="46df8-248">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="46df8-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="46df8-249">Publicar e implementar la aplicación</span><span class="sxs-lookup"><span data-stu-id="46df8-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="46df8-250">Para una implementación de 64 bits:</span><span class="sxs-lookup"><span data-stu-id="46df8-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="46df8-251">Use un SDK de .NET Core de 64 bits para compilar una aplicación de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="46df8-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="46df8-252">Establezca la **Plataforma** en **64 bits** en **Configuración** > **Configuración general** de App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="46df8-253">La aplicación debe usar un plan de servicio básico o superior para habilitar la elección del valor de bits de la plataforma.</span><span class="sxs-lookup"><span data-stu-id="46df8-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="46df8-254">Implementación de la aplicación dependiente del marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="46df8-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46df8-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46df8-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="46df8-256">Seleccione **Compilar** > **Publicar {Nombre de la aplicación}** en la barra de herramientas de Visual Studio o, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="46df8-257">En el cuadro de diálogo **Elegir un destino de publicación**, confirme que está seleccionado **App Service**.</span><span class="sxs-lookup"><span data-stu-id="46df8-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="46df8-258">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="46df8-258">Select **Advanced**.</span></span> <span data-ttu-id="46df8-259">Se abre el cuadro de diálogo **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="46df8-260">En el cuadro de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="46df8-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="46df8-261">Confirme que está seleccionada la configuración de **Versión**.</span><span class="sxs-lookup"><span data-stu-id="46df8-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="46df8-262">Abra la lista desplegable **Modo de implementación** y seleccione **Dependiente de marco de trabajo**.</span><span class="sxs-lookup"><span data-stu-id="46df8-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="46df8-263">Seleccione **Portátil** como **Tiempo de ejecución de destino**.</span><span class="sxs-lookup"><span data-stu-id="46df8-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="46df8-264">Si necesita quitar archivos adicionales tras la implementación, abra **Opciones de publicación de archivos** y active la casilla de verificación para quitar archivos adicionales en el destino.</span><span class="sxs-lookup"><span data-stu-id="46df8-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="46df8-265">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-265">Select **Save**.</span></span>
1. <span data-ttu-id="46df8-266">Cree un sitio nuevo o actualice un sitio existente siguiendo las instrucciones restantes del asistente para publicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="46df8-267">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="46df8-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="46df8-268">En el archivo de proyecto, no especifique [Identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="46df8-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="46df8-269">Desde un shell de comandos, publique la aplicación en Configuración de versión con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="46df8-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="46df8-270">En el ejemplo siguiente, la aplicación está publicada como aplicación dependiente de marco de trabajo:</span><span class="sxs-lookup"><span data-stu-id="46df8-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="46df8-271">Mueva el contenido del directorio *bin/Release/{TARGET FRAMEWORK}/publish* al sitio de App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="46df8-272">Si va a arrastrar el contenido de la carpeta *publish* de su disco duro local o de un recurso compartido de red directamente a App Service en la consola de [Kudu](https://github.com/projectkudu/kudu/wiki), arrastre los archivos a la carpeta `D:\home\site\wwwroot` en la consola de Kudu.</span><span class="sxs-lookup"><span data-stu-id="46df8-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="46df8-273">Implementación de la aplicación independiente</span><span class="sxs-lookup"><span data-stu-id="46df8-273">Deploy the app self-contained</span></span>

<span data-ttu-id="46df8-274">Use Visual Studio o la CLI de .NET Core para una [implementación autocontenida (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="46df8-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46df8-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46df8-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="46df8-276">Seleccione **Compilar** > **Publicar {Nombre de la aplicación}** en la barra de herramientas de Visual Studio o, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="46df8-277">En el cuadro de diálogo **Elegir un destino de publicación**, confirme que está seleccionado **App Service**.</span><span class="sxs-lookup"><span data-stu-id="46df8-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="46df8-278">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="46df8-278">Select **Advanced**.</span></span> <span data-ttu-id="46df8-279">Se abre el cuadro de diálogo **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="46df8-280">En el cuadro de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="46df8-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="46df8-281">Confirme que está seleccionada la configuración de **Versión**.</span><span class="sxs-lookup"><span data-stu-id="46df8-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="46df8-282">Abra la lista desplegable **Modo de implementación** y seleccione **Independiente**.</span><span class="sxs-lookup"><span data-stu-id="46df8-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="46df8-283">Seleccione el entorno de ejecución de destino en la lista desplegable **Tiempo de ejecución de destino**.</span><span class="sxs-lookup"><span data-stu-id="46df8-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="46df8-284">De manera predeterminada, es `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="46df8-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="46df8-285">Si necesita quitar archivos adicionales tras la implementación, abra **Opciones de publicación de archivos** y active la casilla de verificación para quitar archivos adicionales en el destino.</span><span class="sxs-lookup"><span data-stu-id="46df8-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="46df8-286">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="46df8-286">Select **Save**.</span></span>
1. <span data-ttu-id="46df8-287">Cree un sitio nuevo o actualice un sitio existente siguiendo las instrucciones restantes del asistente para publicación.</span><span class="sxs-lookup"><span data-stu-id="46df8-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="46df8-288">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="46df8-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="46df8-289">En el archivo de proyecto, especifique uno o más [identificadores de entorno de ejecución (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="46df8-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="46df8-290">Use `<RuntimeIdentifier>` (en singular) para un único RID o use `<RuntimeIdentifiers>` (en plural) para proporcionar una lista de RID delimitada por puntos y coma.</span><span class="sxs-lookup"><span data-stu-id="46df8-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="46df8-291">En el ejemplo siguiente, se especifica el RID `win-x86`:</span><span class="sxs-lookup"><span data-stu-id="46df8-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="46df8-292">Desde un shell de comandos, publique la aplicación en la configuración de la versión para el entorno de ejecución del host con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="46df8-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="46df8-293">En el ejemplo siguiente, la aplicación está publicada para el RID `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="46df8-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="46df8-294">El RID proporcionado para la opción `--runtime` debe indicarse en la propiedad `<RuntimeIdentifier>` (o `<RuntimeIdentifiers>`) del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="46df8-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="46df8-295">Mueva el contenido del directorio *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* al sitio de App Service.</span><span class="sxs-lookup"><span data-stu-id="46df8-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="46df8-296">Si va a arrastrar el contenido de la carpeta *publish* de su disco duro local o de un recurso compartido de red directamente a App Service en la consola de Kudu, arrastre los archivos a la carpeta `D:\home\site\wwwroot` en la consola de Kudu.</span><span class="sxs-lookup"><span data-stu-id="46df8-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="46df8-297">Configuración del protocolo (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="46df8-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="46df8-298">Los enlaces de protocolo seguro permiten especificar un certificado para usarlo al responder a solicitudes a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="46df8-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="46df8-299">Los enlaces requieren un certificado privado válido ( *.pfx*) que se haya emitido para el nombre de host en cuestión.</span><span class="sxs-lookup"><span data-stu-id="46df8-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="46df8-300">Para obtener más información, consulte [Tutorial: Enlazar un certificado SSL personalizado existente a Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="46df8-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="46df8-301">Transformación de web.config</span><span class="sxs-lookup"><span data-stu-id="46df8-301">Transform web.config</span></span>

<span data-ttu-id="46df8-302">Si necesita transformar *web.config* al realizar la publicación (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="46df8-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46df8-303">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="46df8-303">Additional resources</span></span>

* [<span data-ttu-id="46df8-304">Información general de App Service</span><span class="sxs-lookup"><span data-stu-id="46df8-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="46df8-305">Azure App Service: el mejor lugar para hospedar las aplicaciones .NET (vídeo de introducción de 55 minutos)</span><span class="sxs-lookup"><span data-stu-id="46df8-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="46df8-306">Azure Friday: experiencia de diagnóstico y solución de problemas de Azure App Service (vídeo de 12 minutos)</span><span class="sxs-lookup"><span data-stu-id="46df8-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="46df8-307">Introducción a diagnósticos de Azure App Service</span><span class="sxs-lookup"><span data-stu-id="46df8-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="46df8-308">Azure App Service en Windows Server utiliza [Internet Information Services (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="46df8-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="46df8-309">Los temas siguientes se aplican a la tecnología subyacente de IIS:</span><span class="sxs-lookup"><span data-stu-id="46df8-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="46df8-310">Windows Server: contenido de administradores de TI para versiones anteriores y actuales</span><span class="sxs-lookup"><span data-stu-id="46df8-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
