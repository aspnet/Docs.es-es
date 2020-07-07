---
title: Publicación de una aplicación ASP.NET Core en IIS
author: rick-anderson
description: Obtenga información sobre cómo hospedar una aplicación ASP.NET Core en un servidor IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: 837a66ef36f1394df87d56132e146ef23a5d5659
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407894"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="7cce3-103">Publicación de una aplicación ASP.NET Core en IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="7cce3-104">En este tutorial se muestra cómo hospedar una aplicación ASP.NET Core en un servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="7cce3-105">En este tutorial se describen los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="7cce3-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7cce3-106">Instalación del conjunto de hospedaje de .NET Core en Windows Server</span><span class="sxs-lookup"><span data-stu-id="7cce3-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="7cce3-107">Creación de un sitio de IIS en el Administrador de IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="7cce3-108">Implementación de una aplicación ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7cce3-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7cce3-109">Prerequisites</span></span>

* <span data-ttu-id="7cce3-110">El [SDK de .NET Core](/dotnet/core/sdk) instalado en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7cce3-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="7cce3-111">Windows Server configurado con el rol de servidor **Servidor web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="7cce3-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="7cce3-112">Si el servidor no está configurado para hospedar sitios web con IIS, siga las instrucciones de la sección *Configuración de IIS* del artículo <xref:host-and-deploy/iis/index#iis-configuration> y, después, vuelva a este tutorial.</span><span class="sxs-lookup"><span data-stu-id="7cce3-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="7cce3-113">**La configuración de IIS y la seguridad de los sitios web implican conceptos que no se describen en este tutorial.**</span><span class="sxs-lookup"><span data-stu-id="7cce3-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="7cce3-114">Consulte las instrucciones de IIS en [la documentación de Microsoft IIS](https://www.iis.net/) y el [artículo de ASP.NET Core sobre hospedaje con IIS](xref:host-and-deploy/iis/index) antes de hospedar aplicaciones de producción en IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="7cce3-115">Entre los escenarios importantes para el hospedaje de IIS que no se describen en este tutorial se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="7cce3-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="7cce3-116">Creación de un subárbol del Registro para la protección de datos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="7cce3-117">Configuración de la lista de control de acceso (ACL) del grupo de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="7cce3-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="7cce3-118">Para centrarse en los conceptos de implementación de IIS, en este tutorial se implementa una aplicación sin seguridad HTTPS configurada en IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="7cce3-119">Para obtener más información sobre cómo hospedar una aplicación habilitada para el protocolo HTTPS, vea los temas de seguridad en la sección [Recursos adicionales](#additional-resources) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="7cce3-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="7cce3-120">En el artículo <xref:host-and-deploy/iis/index> se proporciona más información sobre cómo hospedar aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7cce3-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7cce3-121">Instalación del conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7cce3-122">Instale el *conjunto de hospedaje de .NET Core* en el servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="7cce3-123">El lote instala .NET Core Runtime, .NET Core Library y el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7cce3-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7cce3-124">El módulo permite que las aplicaciones ASP.NET Core se ejecuten detrás de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="7cce3-125">Descargue al instalador mediante el vínculo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7cce3-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="7cce3-126">Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)</span><span class="sxs-lookup"><span data-stu-id="7cce3-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="7cce3-127">Ejecute el instalador en el servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="7cce3-128">Reinicie el servidor o ejecute **net stop was /y**, seguido de **net start w3svc** en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7cce3-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7cce3-129">Creación del sitio de IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-129">Create the IIS site</span></span>

1. <span data-ttu-id="7cce3-130">En el servidor IIS, cree una carpeta para que contenga los archivos y las carpetas publicados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7cce3-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7cce3-131">En un paso posterior, la ruta de acceso de la carpeta se proporciona a IIS como la ruta de acceso física a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7cce3-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="7cce3-132">En Administrador de IIS, abra el nodo del servidor en el panel **Conexiones**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7cce3-133">Haga clic con el botón derecho en la carpeta **Sitios**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7cce3-134">Haga clic en **Agregar sitio web** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="7cce3-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7cce3-135">Proporcione el **Nombre del sitio** y establezca la **Ruta de acceso física** a la carpeta de implementación de la aplicación que ha creado.</span><span class="sxs-lookup"><span data-stu-id="7cce3-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="7cce3-136">Proporcione la configuración de **Enlace** y seleccione **Aceptar** para crear el sitio web.</span><span class="sxs-lookup"><span data-stu-id="7cce3-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="7cce3-137">Creación de una aplicación Razor Pages de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="7cce3-138">Siga el tutorial <xref:getting-started> para crear una aplicación Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7cce3-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="7cce3-139">Publicar e implementar la aplicación</span><span class="sxs-lookup"><span data-stu-id="7cce3-139">Publish and deploy the app</span></span>

<span data-ttu-id="7cce3-140">*Publicar una aplicación* significa generar una aplicación compilada que se puede hospedar en un servidor.</span><span class="sxs-lookup"><span data-stu-id="7cce3-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="7cce3-141">*Implementar una aplicación* significa trasladar la aplicación publicada a un sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="7cce3-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="7cce3-142">El paso de publicación lo controla el [SDK de .NET Core](/dotnet/core/sdk), mientras que el paso de implementación se puede controlar mediante distintos enfoques.</span><span class="sxs-lookup"><span data-stu-id="7cce3-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="7cce3-143">En este tutorial se adopta el enfoque de implementación de *carpetas*, donde:</span><span class="sxs-lookup"><span data-stu-id="7cce3-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="7cce3-144">La aplicación se publica en una carpeta.</span><span class="sxs-lookup"><span data-stu-id="7cce3-144">The app is published to a folder.</span></span>
* <span data-ttu-id="7cce3-145">El contenido de la carpeta se mueve a la carpeta del sitio de IIS (la **ruta de acceso física** al sitio en el Administrador de IIS).</span><span class="sxs-lookup"><span data-stu-id="7cce3-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7cce3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cce3-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7cce3-147">Desde el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="7cce3-148">En el cuadro de diálogo **Elegir un destino de publicación**, seleccione la opción de publicación **Carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="7cce3-149">Establezca la ruta de acceso **Recurso compartido de archivos o carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="7cce3-150">Si ha creado una carpeta para el sitio de IIS que está disponible en el equipo de desarrollo como un recurso compartido de red, proporcione la ruta de acceso al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="7cce3-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="7cce3-151">El usuario actual debe tener acceso de escritura para publicar en el recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="7cce3-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="7cce3-152">Si no puede realizar la implementación directamente en la carpeta del sitio de IIS en el servidor IIS, publique en una carpeta de un medio extraíble y mueva físicamente la aplicación publicada a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="7cce3-153">Mueva el contenido de la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish* a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7cce3-154">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="7cce3-155">En un shell de comandos, publique la aplicación en Configuración de versión con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="7cce3-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="7cce3-156">Mueva el contenido de la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish* a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7cce3-157">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7cce3-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7cce3-158">Haga clic con el botón derecho en el proyecto en **Solución** y seleccione **Publicar** > **Publicación en carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="7cce3-159">Establezca la ruta de acceso **Elegir una carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7cce3-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="7cce3-160">Si ha creado una carpeta para el sitio de IIS que está disponible en el equipo de desarrollo como un recurso compartido de red, proporcione la ruta de acceso al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="7cce3-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="7cce3-161">El usuario actual debe tener acceso de escritura para publicar en el recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="7cce3-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="7cce3-162">Si no puede realizar la implementación directamente en la carpeta del sitio de IIS en el servidor IIS, publique en una carpeta de un medio extraíble y mueva físicamente la aplicación publicada a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="7cce3-163">Mueva el contenido de la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish* a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.</span><span class="sxs-lookup"><span data-stu-id="7cce3-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="7cce3-164">Examinar el sitio web</span><span class="sxs-lookup"><span data-stu-id="7cce3-164">Browse the website</span></span>

<span data-ttu-id="7cce3-165">Se puede acceder a la aplicación en un explorador después de que reciba la primera solicitud.</span><span class="sxs-lookup"><span data-stu-id="7cce3-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="7cce3-166">Realice una solicitud a la aplicación en el enlace de punto de conexión que ha establecido en el Administrador de IIS para el sitio.</span><span class="sxs-lookup"><span data-stu-id="7cce3-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7cce3-167">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="7cce3-167">Next steps</span></span>

<span data-ttu-id="7cce3-168">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="7cce3-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7cce3-169">Instalación del conjunto de hospedaje de .NET Core en Windows Server</span><span class="sxs-lookup"><span data-stu-id="7cce3-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="7cce3-170">Creación de un sitio de IIS en el Administrador de IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="7cce3-171">Implementación de una aplicación ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="7cce3-172">Para obtener más información sobre cómo hospedar aplicaciones ASP.NET Core en IIS, vea el artículo Información general de IIS:</span><span class="sxs-lookup"><span data-stu-id="7cce3-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="7cce3-173">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7cce3-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="7cce3-174">Artículos en el conjunto de documentación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="7cce3-175">Artículos relacionados con la implementación de aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cce3-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="7cce3-176">Publicación de una aplicación web en una carpeta mediante Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7cce3-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="7cce3-177">Artículos sobre la configuración HTTPS de IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="7cce3-178">Configuración de SSL en el Administrador de IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="7cce3-179">Procedimientos para configurar SSL en IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="7cce3-180">Artículos sobre IIS y Windows Server</span><span class="sxs-lookup"><span data-stu-id="7cce3-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="7cce3-181">Sitio oficial de Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="7cce3-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7cce3-182">Biblioteca de contenido técnico de Windows Server</span><span class="sxs-lookup"><span data-stu-id="7cce3-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
