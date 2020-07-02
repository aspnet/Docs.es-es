---
title: Implementación continua en Azure con Visual Studio y Git con ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla en Azure App Service con Git para una implementación continua.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 97da88b6fb79944d99b69c92eb611dd0e4e39454
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400172"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="5fd94-103">Implementación continua en Azure con Visual Studio y Git con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fd94-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="5fd94-104">Por [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="5fd94-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="5fd94-105">En este tutorial se muestra cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla desde Visual Studio en Azure App Service mediante una implementación continua.</span><span class="sxs-lookup"><span data-stu-id="5fd94-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="5fd94-106">Vea también [Creación de la primera canalización con Azure Pipelines](/azure/devops/pipelines/get-started-yaml), donde se explica cómo configurar un flujo de trabajo de entrega continua (CD) para [Azure App Service](/azure/app-service/app-service-web-overview) mediante Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="5fd94-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="5fd94-107">Azure Pipelines, un servicio de Azure DevOps Services, simplifica la configuración de una canalización de implementación sólida para publicar actualizaciones de aplicaciones hospedadas en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5fd94-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="5fd94-108">La canalización se puede configurar desde Azure Portal para crear y ejecutar pruebas, implementarlas en un espacio de ensayo y luego implementarlas en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="5fd94-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="5fd94-109">Para realizar este tutorial, necesita una cuenta de Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="5fd94-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="5fd94-110">Para obtener una, [active las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) o [regístrese para una prueba gratuita](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="5fd94-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5fd94-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5fd94-111">Prerequisites</span></span>

<span data-ttu-id="5fd94-112">En este tutorial se da por hecho que está instalado el siguiente software:</span><span class="sxs-lookup"><span data-stu-id="5fd94-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="5fd94-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fd94-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="5fd94-114">[Git](https://git-scm.com/downloads) para Windows</span><span class="sxs-lookup"><span data-stu-id="5fd94-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="5fd94-115">Crear una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fd94-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="5fd94-116">Inicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5fd94-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="5fd94-117">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="5fd94-118">Seleccione la plantilla de proyecto **Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="5fd94-119">Aparece en **Instalados** > **Plantillas** > **Visual C#**  >  **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="5fd94-120">Dé un nombre al proyecto `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="5fd94-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="5fd94-121">Seleccione la opción **Crear nuevo repositorio de Git** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Cuadro de diálogo Nuevo proyecto](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="5fd94-123">En el cuadro de diálogo **Nuevo proyecto ASP.NET Core**, seleccione la plantilla **Vacía** de ASP.NET Core y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Cuadro de diálogo Nuevo proyecto ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="5fd94-125">La versión más reciente de .NET Core es la 2.0.</span><span class="sxs-lookup"><span data-stu-id="5fd94-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="5fd94-126">Ejecutar la aplicación web de forma local</span><span class="sxs-lookup"><span data-stu-id="5fd94-126">Running the web app locally</span></span>

1. <span data-ttu-id="5fd94-127">Cuando Visual Studio haya acabado de crear la aplicación, ejecútela seleccionando **Depurar** > **Iniciar depuración**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="5fd94-128">Como alternativa, presione **F5**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="5fd94-129">Visual Studio y la aplicación nueva pueden tardar un poco en inicializarse.</span><span class="sxs-lookup"><span data-stu-id="5fd94-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="5fd94-130">Una vez completada la operación, el explorador muestra la aplicación en ejecución.</span><span class="sxs-lookup"><span data-stu-id="5fd94-130">Once it's complete, the browser shows the running app.</span></span>

   ![Ventana del explorador en la que aparece la aplicación en ejecución que muestra "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="5fd94-132">Después de revisar la aplicación web en ejecución, cierre el explorador y seleccione el icono "Detener depuración" de la barra de herramientas de Visual Studio para detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fd94-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="5fd94-133">Crear una aplicación web en Azure Portal</span><span class="sxs-lookup"><span data-stu-id="5fd94-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="5fd94-134">Los pasos siguientes le permiten crear una aplicación web en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5fd94-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="5fd94-135">Inicie sesión en [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="5fd94-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="5fd94-136">Seleccione **Nuevo** en la parte superior izquierda de la interfaz del portal.</span><span class="sxs-lookup"><span data-stu-id="5fd94-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="5fd94-137">Seleccione **Web y móvil** > **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal: botón Nuevo: Web y móvil en Marketplace: Botón Aplicación web en Aplicaciones destacadas](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="5fd94-139">En la hoja **Aplicación web**, escriba un valor único para el **nombre del servicio de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Hoja Aplicación web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="5fd94-141">El **nombre de App Service** debe ser único.</span><span class="sxs-lookup"><span data-stu-id="5fd94-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="5fd94-142">El portal aplica esta regla cuando se proporciona el nombre.</span><span class="sxs-lookup"><span data-stu-id="5fd94-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="5fd94-143">Si proporciona un valor diferente, sustituya ese valor por cada aparición de **SampleWebAppDemo** en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fd94-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="5fd94-144">También en la hoja **Aplicación web**, seleccione un **plan o ubicación existente de App Service** o bien cree uno.</span><span class="sxs-lookup"><span data-stu-id="5fd94-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="5fd94-145">Si va a crear un plan, seleccione el plan de tarifa, la ubicación y otras opciones.</span><span class="sxs-lookup"><span data-stu-id="5fd94-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="5fd94-146">Para más información sobre los planes de App Service, consulte [Introducción detallada a los planes de Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="5fd94-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="5fd94-147">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-147">Select **Create**.</span></span> <span data-ttu-id="5fd94-148">Azure aprovisionará e iniciará la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-148">Azure will provision and start the web app.</span></span>

   ![Azure Portal: Hoja SampleWebAppDemo01 de Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="5fd94-150">Habilitar la publicación de Git para la nueva aplicación web</span><span class="sxs-lookup"><span data-stu-id="5fd94-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="5fd94-151">Git es un sistema distribuido de control de versiones que se puede usar para implementar una aplicación web de Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5fd94-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="5fd94-152">El código de aplicación web se almacena en un repositorio de Git local y se implementa en Azure mediante la inserción en un repositorio remoto.</span><span class="sxs-lookup"><span data-stu-id="5fd94-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="5fd94-153">Inicie sesión en [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="5fd94-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="5fd94-154">Seleccione **App Services** para ver una lista los servicios de aplicaciones asociados a su suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="5fd94-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="5fd94-155">Seleccione la aplicación web que creó en la sección anterior de este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fd94-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="5fd94-156">En la hoja **Implementación**, seleccione **Opciones de implementación** > **Elegir origen** > **Repositorio de Git local**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Hoja Configuración: Hoja Origen de implementación: Hoja Elegir origen](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="5fd94-158">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-158">Select **OK**.</span></span>

1. <span data-ttu-id="5fd94-159">Si no ha configurado antes las credenciales de implementación para publicar una aplicación web u otra aplicación de App Service, configúrelas ahora:</span><span class="sxs-lookup"><span data-stu-id="5fd94-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="5fd94-160">Seleccione **Configuración** > **Credenciales de implementación**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="5fd94-161">Se muestra la hoja **Configurar credenciales de implementación**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="5fd94-162">Cree un nombre de usuario y una contraseña.</span><span class="sxs-lookup"><span data-stu-id="5fd94-162">Create a user name and password.</span></span> <span data-ttu-id="5fd94-163">Guarde la contraseña; la necesitará más adelante al configurar Git.</span><span class="sxs-lookup"><span data-stu-id="5fd94-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="5fd94-164">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-164">Select **Save**.</span></span>

1. <span data-ttu-id="5fd94-165">En la hoja **Aplicación web**, seleccione **Configuración** > **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="5fd94-166">La dirección URL del repositorio de Git remoto en el que va a efectuar la implementación aparece en **Dirección URL de Git**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="5fd94-167">Copie el valor de **Dirección URL de Git** para usarlo más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fd94-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: Hoja Propiedades de la aplicación](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="5fd94-169">Publicación de la aplicación web en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5fd94-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="5fd94-170">En esta sección, creará un repositorio de Git local con Visual Studio y lo insertará desde ese repositorio en Azure para implementar la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="5fd94-171">Los pasos son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="5fd94-171">The steps involved include the following:</span></span>

* <span data-ttu-id="5fd94-172">Agrega la configuración de repositorio remoto mediante el valor de dirección URL de GIT, de modo que el repositorio local se pueda implementar en Azure</span><span class="sxs-lookup"><span data-stu-id="5fd94-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="5fd94-173">Confirmar los cambios en el proyecto</span><span class="sxs-lookup"><span data-stu-id="5fd94-173">Commit project changes.</span></span>
* <span data-ttu-id="5fd94-174">Insertar los cambios en el proyecto desde el repositorio local hasta el repositorio remoto en Azure</span><span class="sxs-lookup"><span data-stu-id="5fd94-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="5fd94-175">En el **Explorador de soluciones**, haga clic con el botón derecho en **Solución 'SampleWebAppDemo'** y seleccione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="5fd94-176">Se muestra **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-176">The **Team Explorer** is displayed.</span></span>

   ![Pestaña Team Explorer: Conexión](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="5fd94-178">En **Team Explorer**, seleccione **Inicio** (icono Inicio) > **Configuración** > **Configuración del repositorio**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="5fd94-179">En la sección **Remotos** de **Configuración del repositorio**, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="5fd94-180">Aparece el cuadro de diálogo **Agregar remoto**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="5fd94-181">Establezca el **nombre** del repositorio remoto en **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="5fd94-182">Establezca el valor de **Recuperar** en la **dirección URL de Git** que copió de Azure anteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fd94-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="5fd94-183">Tenga en cuenta que esta es la dirección URL que termina en **.git**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Cuadro de diálogo Editar Azure-SampleApp remoto](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="5fd94-185">Como alternativa, especifique el repositorio remoto desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba el comando.</span><span class="sxs-lookup"><span data-stu-id="5fd94-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="5fd94-186">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5fd94-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="5fd94-187">Seleccione **Inicio** (icono de Inicio) > **Configuración** > **Configuración global**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="5fd94-188">Confirme que se establecen el nombre y la direcciones de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="5fd94-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="5fd94-189">Seleccione **Actualizar** si es necesario.</span><span class="sxs-lookup"><span data-stu-id="5fd94-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="5fd94-190">Seleccione **Inicio** > **Cambios** para volver a la vista **Cambios**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="5fd94-191">Escriba un mensaje de confirmación, como **Inserción inicial 1** y seleccione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="5fd94-192">Esta acción crea una *confirmación* localmente.</span><span class="sxs-lookup"><span data-stu-id="5fd94-192">This action creates a *commit* locally.</span></span>

   ![Pestaña Team Explorer: Conexión](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="5fd94-194">Como alternativa, puede confirmar los cambios desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba los comandos de Git.</span><span class="sxs-lookup"><span data-stu-id="5fd94-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="5fd94-195">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5fd94-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="5fd94-196">Seleccione **Inicio** > **Sincronizar** > **Acciones** > **Abrir símbolo del sistema**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="5fd94-197">El símbolo del sistema se abre en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5fd94-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="5fd94-198">Escriba el siguiente comando en la ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="5fd94-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="5fd94-199">Escriba la contraseña de sus **credenciales de implementación** de Azure que creó anteriormente en Azure.</span><span class="sxs-lookup"><span data-stu-id="5fd94-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="5fd94-200">Este comando inicia el proceso de inserción de los archivos locales del proyecto en Azure.</span><span class="sxs-lookup"><span data-stu-id="5fd94-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="5fd94-201">La salida del comando anterior finaliza con un mensaje que indica que la implementación se efectuó correctamente.</span><span class="sxs-lookup"><span data-stu-id="5fd94-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="5fd94-202">Si se requiere la colaboración en el proyecto, considere la posibilidad de insertarlos en [GitHub](https://github.com) antes de insertarlos en Azure.</span><span class="sxs-lookup"><span data-stu-id="5fd94-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="5fd94-203">Comprobar la implementación activa</span><span class="sxs-lookup"><span data-stu-id="5fd94-203">Verify the Active Deployment</span></span>

<span data-ttu-id="5fd94-204">Compruebe que la transferencia de la aplicación web desde el entorno local a Azure es correcta.</span><span class="sxs-lookup"><span data-stu-id="5fd94-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="5fd94-205">En [Azure Portal](https://portal.azure.com), seleccione la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="5fd94-206">Después, seleccione **Implementación** > **Opciones de implementación**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: Hoja Configuración: Hoja Implementaciones donde se muestra una implementación correcta](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="5fd94-208">Ejecutar la aplicación en Azure</span><span class="sxs-lookup"><span data-stu-id="5fd94-208">Run the app in Azure</span></span>

<span data-ttu-id="5fd94-209">Ahora que la aplicación web se ha implementado en Azure, ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fd94-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="5fd94-210">Esto puede realizarse de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="5fd94-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="5fd94-211">En Azure Portal, busque la hoja de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="5fd94-212">Seleccione **aminar** para ver la aplicación en el explorador predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5fd94-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="5fd94-213">Abra un explorador y escriba la dirección URL de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="5fd94-214">Ejemplo: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="5fd94-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="5fd94-215">Actualizar la aplicación web y volver a publicarla</span><span class="sxs-lookup"><span data-stu-id="5fd94-215">Update the web app and republish</span></span>

<span data-ttu-id="5fd94-216">Después de realizar cambios en el código local, vuelva a publicar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5fd94-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="5fd94-217">En el **Explorador de soluciones** de Visual Studio, abra el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5fd94-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="5fd94-218">En el método `Configure`, modifique el método `Response.WriteAsync` para que aparezca del siguiente modo:</span><span class="sxs-lookup"><span data-stu-id="5fd94-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="5fd94-219">Guarde los cambios en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5fd94-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="5fd94-220">En el **Explorador de soluciones**, haga clic con el botón derecho en **Solución 'SampleWebAppDemo'** y seleccione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="5fd94-221">Se muestra **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="5fd94-222">Escriba un mensaje de confirmación, como `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="5fd94-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="5fd94-223">Presione el botón **Confirmar** para confirmar los cambios del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5fd94-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="5fd94-224">Seleccione **Inicio** > **Sincronizar** > **Acciones** > **Inserción**.</span><span class="sxs-lookup"><span data-stu-id="5fd94-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="5fd94-225">Como alternativa, inserte los cambios desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba un comando de Git.</span><span class="sxs-lookup"><span data-stu-id="5fd94-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="5fd94-226">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5fd94-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="5fd94-227">Ver la aplicación web actualizada en Azure</span><span class="sxs-lookup"><span data-stu-id="5fd94-227">View the updated web app in Azure</span></span>

<span data-ttu-id="5fd94-228">Para ver la aplicación web actualizada, seleccione **Examinar** en la hoja de la aplicación web en Azure Portal o abra un explorador y escriba la dirección URL de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5fd94-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="5fd94-229">Ejemplo: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="5fd94-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fd94-230">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5fd94-230">Additional resources</span></span>

* [<span data-ttu-id="5fd94-231">Creación de la primera canalización con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="5fd94-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="5fd94-232">Proyecto Kudu</span><span class="sxs-lookup"><span data-stu-id="5fd94-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
