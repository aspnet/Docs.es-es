---
title: Publicación de una SignalR aplicación ASP.net Core en Azure App Service
author: bradygaster
description: Obtenga información sobre cómo publicar una SignalR aplicación ASP.net Core en Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 3163a2743c544d0a07d92eb7547e77c3b200ac83
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022542"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="8d5b4-103">Publicación de una SignalR aplicación ASP.net Core en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8d5b4-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="8d5b4-104">Por el [Brady](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="8d5b4-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="8d5b4-105">[Azure App Service](/azure/app-service/app-service-web-overview) es un servicio de plataforma de [informática en la nube de Microsoft](https://azure.microsoft.com/) para hospedar aplicaciones Web, incluidas ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="8d5b4-106">En este artículo se hace referencia a la publicación SignalR de una aplicación ASP.net Core desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="8d5b4-107">Para obtener más información, consulte [ SignalR servicio de Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="8d5b4-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="8d5b4-108">Publicar la aplicación</span><span class="sxs-lookup"><span data-stu-id="8d5b4-108">Publish the app</span></span>

<span data-ttu-id="8d5b4-109">En este artículo se describe la publicación con las herramientas de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="8d5b4-110">Visual Studio Code los usuarios pueden usar [CLI de Azure](/cli/azure) comandos para publicar aplicaciones en Azure.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="8d5b4-111">Para obtener más información, consulte [publicación de una aplicación ASP.net Core en Azure con herramientas de línea de comandos](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8d5b4-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="8d5b4-112">Desde el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="8d5b4-113">Confirme que **App Service** y **crear nuevos** están seleccionados en el cuadro de diálogo **seleccionar un destino de publicación** .</span><span class="sxs-lookup"><span data-stu-id="8d5b4-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="8d5b4-114">Seleccione **crear perfil** en la lista desplegable del botón **publicar** .</span><span class="sxs-lookup"><span data-stu-id="8d5b4-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="8d5b4-115">Escriba la información que se describe en la tabla siguiente en el cuadro de diálogo **crear App Service** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="8d5b4-116">Elemento</span><span class="sxs-lookup"><span data-stu-id="8d5b4-116">Item</span></span>               | <span data-ttu-id="8d5b4-117">Descripción</span><span class="sxs-lookup"><span data-stu-id="8d5b4-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="8d5b4-118">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="8d5b4-118">**Name**</span></span>           | <span data-ttu-id="8d5b4-119">Nombre único de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="8d5b4-120">**Suscripción**</span><span class="sxs-lookup"><span data-stu-id="8d5b4-120">**Subscription**</span></span>   | <span data-ttu-id="8d5b4-121">Suscripción de Azure que usa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="8d5b4-122">**Grupo de recursos**</span><span class="sxs-lookup"><span data-stu-id="8d5b4-122">**Resource Group**</span></span> | <span data-ttu-id="8d5b4-123">Grupo de recursos relacionados a los que pertenece la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="8d5b4-124">**Plan de hospedaje**</span><span class="sxs-lookup"><span data-stu-id="8d5b4-124">**Hosting Plan**</span></span>   | <span data-ttu-id="8d5b4-125">Plan de precios de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="8d5b4-126">Seleccione el \*\* SignalR servicio de Azure\*\* en la lista desplegable **dependencias**  >  **Agregar** :</span><span class="sxs-lookup"><span data-stu-id="8d5b4-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Área de dependencias que muestra la selección de Azure::: no-LOC (Signalr)::: Service en la lista desplegable agregar](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="8d5b4-128">En el cuadro de diálogo \*\* SignalR servicio de Azure\*\* , seleccione **crear una nueva SignalR instancia de servicio de Azure**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="8d5b4-129">Proporcione un **nombre**, un **grupo de recursos**y una **Ubicación**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="8d5b4-130">Vuelva al cuadro de diálogo \*\* SignalR servicio de Azure\*\* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="8d5b4-131">Visual Studio completa las siguientes tareas:</span><span class="sxs-lookup"><span data-stu-id="8d5b4-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="8d5b4-132">Crea un perfil de publicación que contiene la configuración de publicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="8d5b4-133">Crea una *aplicación Web de Azure* con los detalles proporcionados.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="8d5b4-134">Publica la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-134">Publishes the app.</span></span>
* <span data-ttu-id="8d5b4-135">Inicia un explorador, que carga la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="8d5b4-136">El formato de la dirección URL de la aplicación es `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="8d5b4-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="8d5b4-137">Por ejemplo, una aplicación denominada `SignalRChatApp` tiene una dirección URL de `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="8d5b4-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="8d5b4-138">Si se produce un error *de puerta de enlace HTTP 502,2-Bad* al implementar una aplicación que tiene como destino una versión preliminar de .net Core, consulte implementación de la [versión preliminar Azure App Service de ASP.net Core para](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) resolverlo.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="8d5b4-139">Configurar la aplicación en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8d5b4-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="8d5b4-140">*Esta sección solo se aplica a las aplicaciones que no usan el servicio de Azure SignalR .*</span><span class="sxs-lookup"><span data-stu-id="8d5b4-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="8d5b4-141">Si la aplicación usa el servicio de Azure SignalR , la App Service no requiere la configuración de la afinidad de enrutamiento de solicitud de aplicaciones (arr) y los sockets Web descritos en esta sección.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="8d5b4-142">Los clientes conectan sus Sockets web al SignalR servicio de Azure, no directamente a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="8d5b4-143">En el caso de las aplicaciones hospedadas sin el servicio de Azure SignalR , habilite:</span><span class="sxs-lookup"><span data-stu-id="8d5b4-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="8d5b4-144">[Afinidad ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(Arr- cookie ) -for-Azure-web-apps.html) para enrutar las solicitudes de un usuario a la misma instancia de App Service.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="8d5b4-145">La configuración predeterminada es **on**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-145">The default setting is **On**.</span></span>
* <span data-ttu-id="8d5b4-146">[Sockets web](xref:fundamentals/websockets) para permitir que el transporte de sockets web funcione.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="8d5b4-147">El valor predeterminado es **OFF**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="8d5b4-148">En el Azure Portal, vaya a la aplicación web en **App Services**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="8d5b4-149">Abra **configuración**  >  **General**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="8d5b4-150">Establezca **Sockets web** en **activado**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="8d5b4-151">Compruebe que la **afinidad ARR** está establecida en **on**.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="8d5b4-152">Límites del plan de App Service</span><span class="sxs-lookup"><span data-stu-id="8d5b4-152">App Service Plan limits</span></span>

<span data-ttu-id="8d5b4-153">Los sockets web y otros transportes se limitan en función del plan de App Service seleccionado.</span><span class="sxs-lookup"><span data-stu-id="8d5b4-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="8d5b4-154">Para obtener más información, consulte los límites de *azure Cloud Services* y los límites de *App Service* de las secciones de los límites [, cuotas y restricciones de suscripción y servicios de Azure](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="8d5b4-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d5b4-155">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8d5b4-155">Additional resources</span></span>

* [<span data-ttu-id="8d5b4-156">¿Qué es el servicio de Azure SignalR ?</span><span class="sxs-lookup"><span data-stu-id="8d5b4-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8d5b4-157">Publicar una aplicación ASP.NET Core en Azure con las herramientas de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="8d5b4-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="8d5b4-158">Hospedar e implementar ASP.NET Core aplicaciones de vista previa en Azure</span><span class="sxs-lookup"><span data-stu-id="8d5b4-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
