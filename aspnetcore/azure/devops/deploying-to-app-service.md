---
title: 'Implementación de una aplicación en App Service: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Implemente una aplicación ASP.NET Core en Azure App Service, el primer paso para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 31cd1f6ac8e1243b25569ae34ea252185bbf6467
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625484"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="4c8ce-103">Implementación de una aplicación en App Service</span><span class="sxs-lookup"><span data-stu-id="4c8ce-103">Deploy an app to App Service</span></span>

<span data-ttu-id="4c8ce-104">[Azure App Service](/azure/app-service/) es la plataforma de hospedaje web de Azure.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="4c8ce-105">La implementación de una aplicación web en Azure App Service puede realizarse manualmente o mediante un proceso automatizado.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="4c8ce-106">En esta sección de la guía se describen los métodos de implementación que se pueden desencadenar manualmente o mediante un script con la línea de comandos o que se desencadenan manualmente con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="4c8ce-107">En esta sección, se realizarán las siguientes tareas:</span><span class="sxs-lookup"><span data-stu-id="4c8ce-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="4c8ce-108">Descargar y compilar la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-108">Download and build the sample app.</span></span>
* <span data-ttu-id="4c8ce-109">Crear una aplicación web de Azure App Service con Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="4c8ce-110">Implementar la aplicación de ejemplo en Azure con Git.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="4c8ce-111">Implementar un cambio en la aplicación mediante Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="4c8ce-112">Agregar un espacio de ensayo a la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="4c8ce-113">Implementar una actualización en el espacio de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="4c8ce-114">Intercambiar los espacios de ensayo y producción.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="4c8ce-115">Descargar y probar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4c8ce-115">Download and test the app</span></span>

<span data-ttu-id="4c8ce-116">La aplicación que se usa en esta guía es una aplicación ASP.NET Core pregenerada, [lector de fuentes simple](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="4c8ce-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="4c8ce-117">Se trata de una aplicación de Razor Pages que usa la API `Microsoft.SyndicationFeed.ReaderWriter` para recuperar una fuente RSS/Atom y mostrar las noticias en una lista.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="4c8ce-118">No dude en revisar el código, pero es importante entender que esta aplicación no tiene nada de extraordinario.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="4c8ce-119">Es simplemente una aplicación de ASP.NET Core con fines ilustrativos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="4c8ce-120">Desde un shell de comandos, descargue el código, compile el proyecto y ejecútelo como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="4c8ce-121">*Nota: Los usuarios de Linux/macOS deben realizar los cambios adecuados en las rutas de acceso, por ejemplo, mediante una barra diagonal (`/`), en lugar de la barra diagonal inversa (`\`).*</span><span class="sxs-lookup"><span data-stu-id="4c8ce-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="4c8ce-122">Clone el código en una carpeta del equipo local.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="4c8ce-123">Cambie la carpeta de trabajo a la carpeta *simple-feed-reader* que se creó.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="4c8ce-124">Restaure los paquetes y compile la solución.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="4c8ce-125">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![El comando de ejecución de dotnet es correcto](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="4c8ce-127">Abra un explorador y vaya a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="4c8ce-128">La aplicación permite escribir o pegar una dirección URL de fuente de distribución y ver una lista de elementos de noticias.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Aplicación que muestra el contenido de una fuente RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="4c8ce-130">Una vez que esté satisfecho de que la aplicación funcione correctamente, ciérrela presionando **Ctrl**+**C** en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="4c8ce-131">Crear la aplicación web de Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4c8ce-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="4c8ce-132">Para implementar la aplicación, debe crear una [aplicación web](/azure/app-service/app-service-web-overview) de App Service.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="4c8ce-133">Después de crear la aplicación web, debe implementarla en el equipo local con Git.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="4c8ce-134">Inicie sesión en [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="4c8ce-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="4c8ce-135">Nota: Cuando inicie sesión por primera vez, Cloud Shell le pedirá que cree una cuenta de almacenamiento para los archivos de configuración.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="4c8ce-136">Acepte los valores predeterminados o proporcione un nombre único.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="4c8ce-137">Use Cloud Shell para los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="4c8ce-138">a.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-138">a.</span></span> <span data-ttu-id="4c8ce-139">Declare una variable para almacenar el nombre de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="4c8ce-140">El nombre debe ser único para usarse en la dirección URL predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="4c8ce-141">El uso de la función Bash `$RANDOM` para construir el nombre garantiza la exclusividad y tiene como resultado el formato `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="4c8ce-142">b.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-142">b.</span></span> <span data-ttu-id="4c8ce-143">Cree un grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-143">Create a resource group.</span></span> <span data-ttu-id="4c8ce-144">Los grupos de recursos proporcionan un medio para agregar recursos de Azure para administrarlos como un grupo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="4c8ce-145">El comando `az` invoca la [CLI de Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="4c8ce-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="4c8ce-146">La CLI se puede ejecutar localmente, pero su uso en Cloud Shell ahorra tiempo y configuración.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="4c8ce-147">c.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-147">c.</span></span> <span data-ttu-id="4c8ce-148">Cree un plan de App Service en el nivel S1.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="4c8ce-149">Un plan de App Service es una agrupación de aplicaciones web que comparten el mismo plan de tarifa.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="4c8ce-150">El nivel S1 no es gratuito, pero es necesario para la característica de espacios de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="4c8ce-151">d.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-151">d.</span></span> <span data-ttu-id="4c8ce-152">Cree el recurso de la aplicación web con el plan de App Service en el mismo grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="4c8ce-153">e.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-153">e.</span></span> <span data-ttu-id="4c8ce-154">Cree las credenciales de implementación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-154">Set the deployment credentials.</span></span> <span data-ttu-id="4c8ce-155">Estas credenciales de implementación se aplican a todas las aplicaciones web de su suscripción.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="4c8ce-156">No utilice caracteres especiales en el nombre de usuario.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="4c8ce-157">f.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-157">f.</span></span> <span data-ttu-id="4c8ce-158">Configure la aplicación web para que acepte implementaciones de Git local y muestre la *dirección URL de implementación de Git*.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="4c8ce-159">**Tenga en cuenta esta dirección URL como referencia más adelante**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="4c8ce-160">g.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-160">g.</span></span> <span data-ttu-id="4c8ce-161">Muestre la dirección *URL de la aplicación web*.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-161">Display the *web app URL*.</span></span> <span data-ttu-id="4c8ce-162">Vaya a esta dirección URL para ver la aplicación web en blanco.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="4c8ce-163">**Tenga en cuenta esta dirección URL como referencia más adelante**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="4c8ce-164">Con un shell de comandos en el equipo local, vaya a la carpeta de proyecto de la aplicación web (por ejemplo, `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="4c8ce-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="4c8ce-165">Ejecute los siguientes comandos para configurar Git a fin de que envíe cambios a la dirección URL de implementación:</span><span class="sxs-lookup"><span data-stu-id="4c8ce-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="4c8ce-166">a.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-166">a.</span></span> <span data-ttu-id="4c8ce-167">Agregue la dirección URL remota al repositorio local.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="4c8ce-168">b.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-168">b.</span></span> <span data-ttu-id="4c8ce-169">Inserte la rama *maestra* local en la rama *maestra* de *azure-prod* remoto.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="4c8ce-170">Se le pedirán las credenciales de implementación que creó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="4c8ce-171">Observe el resultado en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-171">Observe the output in the command shell.</span></span> <span data-ttu-id="4c8ce-172">Azure crea la aplicación ASP.NET Core de forma remota.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="4c8ce-173">En un explorador, vaya a la *dirección URL de la aplicación web* y tenga en cuenta que la aplicación se ha compilado e implementado.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="4c8ce-174">Se pueden confirmar otros cambios en el repositorio local de Git con `git commit`.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="4c8ce-175">Estos cambios se insertan en Azure con el comando `git push` anterior.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="4c8ce-176">Implementación con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c8ce-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="4c8ce-177">*Nota: Esta sección se aplica solo a Windows. Los usuarios de Linux y macOS deben realizar el cambio descrito en el paso 2 a continuación. Guarde el archivo y confirme el cambio en el repositorio local con `git commit`. Por último, inserte el cambio con `git push`, como en la primera sección.*</span><span class="sxs-lookup"><span data-stu-id="4c8ce-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="4c8ce-178">La aplicación ya se ha implementado desde el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="4c8ce-179">Vamos a usar las herramientas integradas de Visual Studio para implementar una actualización de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="4c8ce-180">En segundo plano, Visual Studio consigue lo mismo que las herramientas de línea de comandos, pero dentro de la interfaz de usuario familiar de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="4c8ce-181">Abra *SimpleFeedReader.sln* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="4c8ce-182">En el Explorador de soluciones, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="4c8ce-183">Cambio de `<h2>Simple Feed Reader</h2>` a `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="4c8ce-184">Presione **Ctrl**+**Mayús**+**B** para compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="4c8ce-185">En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, a continuación, en **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Captura de pantalla que muestra el clic con el botón derecho, Publicar](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="4c8ce-187">Visual Studio puede crear un nuevo recurso de App Service, pero esta actualización se publicará en la implementación existente.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="4c8ce-188">En el cuadro de diálogo **Elegir un destino de publicación**, seleccione **App Service** en la lista de la izquierda y, a continuación, seleccione **Seleccionar existentes**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="4c8ce-189">Haga clic en **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-189">Click **Publish**.</span></span>
6. <span data-ttu-id="4c8ce-190">En el cuadro de diálogo **App Service**, confirme que la cuenta profesional o de Microsoft utilizada para crear la suscripción de Azure se muestre en la esquina superior derecha.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="4c8ce-191">Si no es así, haga clic en el menú desplegable y agréguelo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="4c8ce-192">Conforme que se selecciona la **suscripción** de Azure correcta.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="4c8ce-193">Para **Vista**, seleccione **Grupo de recursos**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="4c8ce-194">Expanda el grupo de recursos **AzureTutorial** y, a continuación, seleccione la aplicación web existente.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="4c8ce-195">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-195">Click **OK**.</span></span>

    ![Captura de pantalla que muestra el cuadro de diálogo Publicar App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="4c8ce-197">Visual Studio compila e implementa la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="4c8ce-198">Navegue hasta la dirección URL de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-198">Browse to the web app URL.</span></span> <span data-ttu-id="4c8ce-199">Valide que la modificación del elemento `<h2>` esté activa.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-199">Validate that the `<h2>` element modification is live.</span></span>

![La aplicación con el título modificado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="4c8ce-201">Ranuras de implementación</span><span class="sxs-lookup"><span data-stu-id="4c8ce-201">Deployment slots</span></span>

<span data-ttu-id="4c8ce-202">Las ranuras de implementación admiten el almacenamiento provisional de los cambios sin afectar a la aplicación que se ejecuta en producción.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="4c8ce-203">Una vez que un equipo de control de calidad valida la versión de ensayo de la aplicación, se pueden intercambiar los espacios de producción y de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="4c8ce-204">La aplicación en almacenamiento provisional se promueve a producción de esta manera.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="4c8ce-205">En los pasos siguientes se crea un espacio de ensayo, se implementan algunos cambios en él y se intercambia el espacio de ensayo con producción después de la comprobación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="4c8ce-206">Inicie sesión en [Azure Cloud Shell](https://shell.azure.com/bash), si aún no ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="4c8ce-207">Cree el espacio de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-207">Create the staging slot.</span></span>

    <span data-ttu-id="4c8ce-208">a.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-208">a.</span></span> <span data-ttu-id="4c8ce-209">Cree una ranura de implementación con el nombre *almacenamiento provisional*.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="4c8ce-210">b.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-210">b.</span></span> <span data-ttu-id="4c8ce-211">Configure el espacio de ensayo para usar la implementación de Git local y obtener la dirección URL de implementación de **almacenamiento provisional**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="4c8ce-212">**Tenga en cuenta esta dirección URL como referencia más adelante**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="4c8ce-213">c.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-213">c.</span></span> <span data-ttu-id="4c8ce-214">Muestre la dirección URL del espacio de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-214">Display the staging slot's URL.</span></span> <span data-ttu-id="4c8ce-215">Vaya a la dirección URL para ver el espacio de ensayo vacío.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="4c8ce-216">**Tenga en cuenta esta dirección URL como referencia más adelante**.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="4c8ce-217">En un editor de texto o Visual Studio, vuelva a modificar *Pages/Index.cshtml* para que el elemento `<h2>` lea `<h2>Simple Feed Reader - V3</h2>` y guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="4c8ce-218">Confirme el archivo en el repositorio de Git local con la página **Cambios** de la pestaña *Team Explorer* de Visual Studio, o bien especifique lo siguiente con el shell de comandos del equipo local:</span><span class="sxs-lookup"><span data-stu-id="4c8ce-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="4c8ce-219">Con el shell de comandos del equipo local, agregue la dirección URL de implementación de ensayo como un Git remoto y envíe los cambios confirmados:</span><span class="sxs-lookup"><span data-stu-id="4c8ce-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="4c8ce-220">a.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-220">a.</span></span> <span data-ttu-id="4c8ce-221">Agregue la dirección URL remota para el almacenamiento provisional en el repositorio local de Git.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="4c8ce-222">b.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-222">b.</span></span> <span data-ttu-id="4c8ce-223">Inserte la rama *maestra* local en la rama *maestra* de *azure-staging* remoto.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="4c8ce-224">Espere mientras Azure compila e implementa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="4c8ce-225">Para comprobar que V3 se ha implementado en el espacio de ensayo, abra dos ventanas del explorador.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="4c8ce-226">En una ventana, vaya a la dirección URL de la aplicación web original.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="4c8ce-227">En la otra ventana, vaya a la dirección URL de la aplicación web de almacenamiento provisional.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="4c8ce-228">La dirección URL de producción ofrece servicio a la versión 2 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="4c8ce-229">La dirección URL de almacenamiento provisional ofrece servicio a la versión 3 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-229">The staging URL serves V3 of the app.</span></span>

    ![Captura de pantalla que compara las ventanas del explorador](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="4c8ce-231">En Cloud Shell, intercambie el espacio de ensayo verificado o preparado a producción.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="4c8ce-232">Para comprobar que se ha producido el intercambio, actualice las dos ventanas del explorador.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparar las ventanas del explorador después del intercambio](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="4c8ce-234">Resumen</span><span class="sxs-lookup"><span data-stu-id="4c8ce-234">Summary</span></span>

<span data-ttu-id="4c8ce-235">En esta sección, se completaron las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4c8ce-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="4c8ce-236">Se descargó y compiló la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="4c8ce-237">Se creó una aplicación web de Azure App Service con Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="4c8ce-238">Se implementó la aplicación de ejemplo en Azure con Git.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="4c8ce-239">Se implementó un cambio en la aplicación mediante Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="4c8ce-240">Se agregó un espacio de ensayo a la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="4c8ce-241">Se implementó una actualización en el espacio de ensayo.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="4c8ce-242">Se intercambiaron los espacios de ensayo y producción.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="4c8ce-243">En la siguiente sección, aprenderá a crear una canalización de DevOps con Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="4c8ce-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="4c8ce-244">Lecturas adicionales</span><span class="sxs-lookup"><span data-stu-id="4c8ce-244">Additional reading</span></span>

* [<span data-ttu-id="4c8ce-245">Introducción a Web Apps</span><span class="sxs-lookup"><span data-stu-id="4c8ce-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="4c8ce-246">Creación de una aplicación .NET Core y SQL Database en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4c8ce-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="4c8ce-247">Configuración de credenciales de implementación para Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4c8ce-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="4c8ce-248">Configuración de entornos de ensayo en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4c8ce-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
