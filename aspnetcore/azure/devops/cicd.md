---
title: 'Integración e implementación continuas: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Integración e implementación continuas en DevOps con ASP.NET Core y Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: azure/devops/cicd
ms.openlocfilehash: 0edded18d766d6f2af08f6be5dbecbfd52a14a35
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400562"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="0a207-103">Integración e implementación continuas</span><span class="sxs-lookup"><span data-stu-id="0a207-103">Continuous integration and deployment</span></span>

<span data-ttu-id="0a207-104">En el capítulo anterior se ha creado un repositorio de Git local para la aplicación Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="0a207-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="0a207-105">En este capítulo se va a publicar ese código en un repositorio de GitHub y a crear una canalización de Azure DevOps Services mediante Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="0a207-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="0a207-106">La canalización habilita las compilaciones y las implementaciones continuas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a207-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="0a207-107">Cualquier confirmación en el repositorio de GitHub desencadena una compilación y una implementación en el espacio de ensayo de la aplicación web de Azure.</span><span class="sxs-lookup"><span data-stu-id="0a207-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="0a207-108">En esta sección se van a realizar las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a207-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="0a207-109">Publicar el código de la aplicación en GitHub</span><span class="sxs-lookup"><span data-stu-id="0a207-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="0a207-110">Desconectar la implementación de Git local</span><span class="sxs-lookup"><span data-stu-id="0a207-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="0a207-111">Crear una organización de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="0a207-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="0a207-112">Crear un proyecto de equipo en Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="0a207-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="0a207-113">Crear una definición de compilación</span><span class="sxs-lookup"><span data-stu-id="0a207-113">Create a build definition</span></span>
* <span data-ttu-id="0a207-114">Creación de una canalización de versión</span><span class="sxs-lookup"><span data-stu-id="0a207-114">Create a release pipeline</span></span>
* <span data-ttu-id="0a207-115">Confirmar cambios en GitHub e implementarlos automáticamente en Azure</span><span class="sxs-lookup"><span data-stu-id="0a207-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="0a207-116">Examinar la canalización de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="0a207-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="0a207-117">Publicar el código de la aplicación en GitHub</span><span class="sxs-lookup"><span data-stu-id="0a207-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="0a207-118">Abra una ventana del explorador y vaya a `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="0a207-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="0a207-119">Haga clic en la lista desplegable **+** del encabezado y seleccione **Nuevo repositorio**:</span><span class="sxs-lookup"><span data-stu-id="0a207-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Opción Nuevo repositorio de GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="0a207-121">Seleccione la cuenta en la lista desplegable **Propietario** y escriba *simple-feed-reader* en el cuadro de texto **Nombre del repositorio**.</span><span class="sxs-lookup"><span data-stu-id="0a207-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="0a207-122">Haga clic en el botón **Crear repositorio**.</span><span class="sxs-lookup"><span data-stu-id="0a207-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="0a207-123">Abra el shell de comandos del equipo local.</span><span class="sxs-lookup"><span data-stu-id="0a207-123">Open your local machine's command shell.</span></span> <span data-ttu-id="0a207-124">Vaya al directorio en el que está almacenado el repositorio de Git *simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="0a207-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="0a207-125">Cambie el nombre del elemento *origin* existente remoto a *upstream*.</span><span class="sxs-lookup"><span data-stu-id="0a207-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="0a207-126">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0a207-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="0a207-127">Agregue un nuevo elemento *origin* remoto que apunte a la copia del repositorio en GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="0a207-128">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0a207-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="0a207-129">Publique el repositorio de Git local en el repositorio de GitHub recién creado.</span><span class="sxs-lookup"><span data-stu-id="0a207-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="0a207-130">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0a207-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="0a207-131">Abra una ventana del explorador y vaya a `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="0a207-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="0a207-132">Valide que el código aparece en el repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="0a207-133">Desconectar la implementación de Git local</span><span class="sxs-lookup"><span data-stu-id="0a207-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="0a207-134">Quite la implementación de Git local con los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="0a207-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="0a207-135">Azure Pipelines (un servicio de Azure DevOps) reemplaza y aumenta esa funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="0a207-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="0a207-136">Abra [Azure Portal](https://portal.azure.com/) y vaya a la aplicación web de *almacenamiento provisional (mywebapp\<unique_number\>/staging)* .</span><span class="sxs-lookup"><span data-stu-id="0a207-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="0a207-137">La aplicación web se puede encontrar rápidamente si se escribe *staging* en el cuadro de búsqueda del portal:</span><span class="sxs-lookup"><span data-stu-id="0a207-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![Término de búsqueda de la aplicación web staging](media/cicd/portal-search-box.png)

1. <span data-ttu-id="0a207-139">Haga clic en **Centro de implementación**.</span><span class="sxs-lookup"><span data-stu-id="0a207-139">Click **Deployment Center**.</span></span> <span data-ttu-id="0a207-140">Aparece un nuevo panel.</span><span class="sxs-lookup"><span data-stu-id="0a207-140">A new panel appears.</span></span> <span data-ttu-id="0a207-141">Haga clic en **Desconectar** para quitar la configuración de control de código fuente de Git local agregada en el capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="0a207-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="0a207-142">Para confirmar la operación de eliminación, haga clic en el botón **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a207-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="0a207-143">Vaya a la instancia de App Service *mywebapp<número_único>* .</span><span class="sxs-lookup"><span data-stu-id="0a207-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="0a207-144">Como recordatorio, el cuadro de búsqueda del portal se puede usar para buscar rápidamente la instancia de App Service.</span><span class="sxs-lookup"><span data-stu-id="0a207-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="0a207-145">Haga clic en **Centro de implementación**.</span><span class="sxs-lookup"><span data-stu-id="0a207-145">Click **Deployment Center**.</span></span> <span data-ttu-id="0a207-146">Aparece un nuevo panel.</span><span class="sxs-lookup"><span data-stu-id="0a207-146">A new panel appears.</span></span> <span data-ttu-id="0a207-147">Haga clic en **Desconectar** para quitar la configuración de control de código fuente de Git local agregada en el capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="0a207-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="0a207-148">Para confirmar la operación de eliminación, haga clic en el botón **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a207-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="0a207-149">Crear una organización de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="0a207-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="0a207-150">Abra un explorador y vaya a la [página de creación de organización de Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="0a207-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="0a207-151">Escriba un nombre único en el cuadro de texto **Elija un nombre fácil de recordar** para formar la dirección URL para acceder a la organización de Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="0a207-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="0a207-152">Seleccione el botón de radio **Git**, ya que el código está hospedado en un repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="0a207-153">Haga clic en el botón **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-153">Click the **Continue** button.</span></span> <span data-ttu-id="0a207-154">Tras una breve espera, se crean una cuenta y un proyecto de equipo que se denomina *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="0a207-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Página de creación de organización de Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="0a207-156">Abra el correo electrónico de confirmación que indica que la organización y el proyecto de Azure DevOps están listos para su uso.</span><span class="sxs-lookup"><span data-stu-id="0a207-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="0a207-157">Haga clic en el botón **Inicie su proyecto**:</span><span class="sxs-lookup"><span data-stu-id="0a207-157">Click the **Start your project** button:</span></span>

    ![Botón Inicie su proyecto](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="0a207-159">Se abre un explorador para *\<account_name\>.visualstudio.com*.</span><span class="sxs-lookup"><span data-stu-id="0a207-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="0a207-160">Haga clic en el vínculo *MyFirstProject* para empezar a configurar la canalización de DevOps del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a207-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="0a207-161">Configurar la canalización de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="0a207-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="0a207-162">Hay tres pasos diferentes que se deben realizar.</span><span class="sxs-lookup"><span data-stu-id="0a207-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="0a207-163">La realización de los pasos de las tres secciones siguientes da lugar a una canalización de DevOps operativa.</span><span class="sxs-lookup"><span data-stu-id="0a207-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="0a207-164">Conceder acceso de Azure DevOps al repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="0a207-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="0a207-165">Expanda el acordeón **o compilar código desde un repositorio externo**.</span><span class="sxs-lookup"><span data-stu-id="0a207-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="0a207-166">Haga clic en el botón **Configurar compilación**:</span><span class="sxs-lookup"><span data-stu-id="0a207-166">Click the **Setup Build** button:</span></span>

    ![Botón de configuración de compilación](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="0a207-168">Seleccione la opción **GitHub** en la sección **Seleccionar un origen**:</span><span class="sxs-lookup"><span data-stu-id="0a207-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Seleccionar un origen - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="0a207-170">Se necesita autorización para que Azure DevOps pueda acceder al repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="0a207-171">Escriba *<nombreUsuario_GitHub> GitHub connection* en el cuadro de texto **Nombre de conexión**.</span><span class="sxs-lookup"><span data-stu-id="0a207-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="0a207-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0a207-172">For example:</span></span>

    ![Nombre de conexión de GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="0a207-174">Si la autenticación en dos fases está habilitada en la cuenta de GitHub, se requiere un token de acceso personal.</span><span class="sxs-lookup"><span data-stu-id="0a207-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="0a207-175">En ese caso, haga clic en el vínculo **Autorizar con un token de acceso personal de GitHub**.</span><span class="sxs-lookup"><span data-stu-id="0a207-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="0a207-176">Vea las [instrucciones oficiales de creación de tokens de acceso personales de GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obtener ayuda.</span><span class="sxs-lookup"><span data-stu-id="0a207-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="0a207-177">Solo se necesita el ámbito *repo* de permisos.</span><span class="sxs-lookup"><span data-stu-id="0a207-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="0a207-178">De lo contrario, haga clic en el botón **Autorizar el uso de OAuth**.</span><span class="sxs-lookup"><span data-stu-id="0a207-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="0a207-179">Cuando se le pida, inicie sesión en la cuenta de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="0a207-180">Luego, seleccione Autorizar para conceder acceso a la organización de Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="0a207-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="0a207-181">Si la operación se realiza correctamente, se crea un nuevo punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="0a207-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="0a207-182">Haga clic en el botón de puntos suspensivos situado junto al botón **Repositorio**.</span><span class="sxs-lookup"><span data-stu-id="0a207-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="0a207-183">En la lista, seleccione el repositorio *<nombreUsuario_GitHub>/simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="0a207-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="0a207-184">Haga clic en el botón **Seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="0a207-185">Seleccione la rama *master* de la lista desplegable **Rama predeterminada para compilaciones manuales y programadas**.</span><span class="sxs-lookup"><span data-stu-id="0a207-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="0a207-186">Haga clic en el botón **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-186">Click the **Continue** button.</span></span> <span data-ttu-id="0a207-187">Aparece la página de selección de plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a207-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="0a207-188">Creación de la definición de compilación</span><span class="sxs-lookup"><span data-stu-id="0a207-188">Create the build definition</span></span>

1. <span data-ttu-id="0a207-189">En la página de selección de plantilla, escriba *ASP.NET Core* en el cuadro de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="0a207-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![Búsqueda de ASP.NET Core en la página de plantilla](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="0a207-191">Aparecen los resultados de búsqueda de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a207-191">The template search results appear.</span></span> <span data-ttu-id="0a207-192">Mantenga el mouse sobre la plantilla **ASP.NET Core** y haga clic en el botón **Aplicar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="0a207-193">Aparece la pestaña **Tareas** de la definición de compilación.</span><span class="sxs-lookup"><span data-stu-id="0a207-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="0a207-194">Haga clic en la pestaña **Desencadenadores**.</span><span class="sxs-lookup"><span data-stu-id="0a207-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="0a207-195">Active la casilla **Habilitar la integración continua**.</span><span class="sxs-lookup"><span data-stu-id="0a207-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="0a207-196">En la sección **Filtros de rama**, confirme que la lista desplegable **Tipo** está establecida en *Incluir*.</span><span class="sxs-lookup"><span data-stu-id="0a207-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="0a207-197">Establezca la lista desplegable **Especificación de rama** en *master*.</span><span class="sxs-lookup"><span data-stu-id="0a207-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Habilitación de la configuración de integración continua](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="0a207-199">Esta configuración hace que se desencadene una compilación cada vez que se inserta cualquier cambio en la rama *master* del repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="0a207-200">La integración continua se prueba en la sección [Confirmar cambios en GitHub e implementar automáticamente en Azure](#commit-changes-to-github-and-automatically-deploy-to-azure).</span><span class="sxs-lookup"><span data-stu-id="0a207-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="0a207-201">Haga clic en el botón **Guardar y poner en cola** y seleccione la opción **Guardar**:</span><span class="sxs-lookup"><span data-stu-id="0a207-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Botón Guardar](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="0a207-203">Aparece el siguiente cuadro de diálogo modal:</span><span class="sxs-lookup"><span data-stu-id="0a207-203">The following modal dialog appears:</span></span>

    ![Guardado de la definición de compilación - cuadro de diálogo modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="0a207-205">Use la carpeta predeterminada de *\\* y haga clic en el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="0a207-206">Creación de la canalización de versión</span><span class="sxs-lookup"><span data-stu-id="0a207-206">Create the release pipeline</span></span>

1. <span data-ttu-id="0a207-207">Haga clic en la pestaña **Versiones** del proyecto de equipo.</span><span class="sxs-lookup"><span data-stu-id="0a207-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="0a207-208">Haga clic en el botón **Nueva canalización**.</span><span class="sxs-lookup"><span data-stu-id="0a207-208">Click the **New pipeline** button.</span></span>

    ![Pestaña Versiones - botón de nueva definición](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="0a207-210">Aparece el panel de selección de plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a207-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="0a207-211">En la página de selección de plantilla, escriba *App Service* en el cuadro de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="0a207-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Cuadro de búsqueda de plantilla de canalización de versión](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="0a207-213">Aparecen los resultados de búsqueda de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a207-213">The template search results appear.</span></span> <span data-ttu-id="0a207-214">Mantenga el mouse sobre la plantilla **Implementación de Azure App Service con espacio** y haga clic en el botón **Aplicar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="0a207-215">Aparece la pestaña **Canalización** de la canalización de versión.</span><span class="sxs-lookup"><span data-stu-id="0a207-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Pestaña Canalización de la canalización de versión](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="0a207-217">Haga clic en el botón **Agregar** del cuadro **Artefactos**.</span><span class="sxs-lookup"><span data-stu-id="0a207-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="0a207-218">Aparece el panel **Agregar artefacto**:</span><span class="sxs-lookup"><span data-stu-id="0a207-218">The **Add artifact** panel appears:</span></span>

    ![Canalización de versión - panel Agregar artefacto](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="0a207-220">Seleccione el icono **Compilación** de la sección **Tipo de origen**.</span><span class="sxs-lookup"><span data-stu-id="0a207-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="0a207-221">Este tipo permite la vinculación de la canalización de versión a la definición de compilación.</span><span class="sxs-lookup"><span data-stu-id="0a207-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="0a207-222">Seleccione *MyFirstProject* en la lista desplegable **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0a207-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="0a207-223">Seleccione el nombre de la definición de compilación, *MyFirstProject-ASP.NET Core-CI*, en la lista desplegable **Origen (definición de compilación)** .</span><span class="sxs-lookup"><span data-stu-id="0a207-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="0a207-224">En la lista desplegable *Versión predeterminada*, seleccione **Más reciente**.</span><span class="sxs-lookup"><span data-stu-id="0a207-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="0a207-225">Esta opción compila los artefactos generados por la última ejecución de la definición de compilación.</span><span class="sxs-lookup"><span data-stu-id="0a207-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="0a207-226">Reemplace el texto del cuadro de texto **Alias de origen** por *Drop*.</span><span class="sxs-lookup"><span data-stu-id="0a207-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="0a207-227">Haga clic en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-227">Click the **Add** button.</span></span> <span data-ttu-id="0a207-228">La sección **Artefactos** se actualiza para mostrar los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a207-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="0a207-229">Haga clic en el icono de rayo para habilitar las implementaciones continuas:</span><span class="sxs-lookup"><span data-stu-id="0a207-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Canalización de versión Artefactos - icono de rayo](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="0a207-231">Con esta opción habilitada, se produce una implementación cada vez que hay una nueva compilación disponible.</span><span class="sxs-lookup"><span data-stu-id="0a207-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="0a207-232">Aparece un panel **Desencadenador de implementación continua** a la derecha.</span><span class="sxs-lookup"><span data-stu-id="0a207-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="0a207-233">Haga clic en el botón de alternancia para habilitar la característica.</span><span class="sxs-lookup"><span data-stu-id="0a207-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="0a207-234">No es necesario habilitar **Desencadenador de solicitud de incorporación de cambios**.</span><span class="sxs-lookup"><span data-stu-id="0a207-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="0a207-235">Haga clic en la lista desplegable **Agregar** de la sección **Filtros de rama de compilación**.</span><span class="sxs-lookup"><span data-stu-id="0a207-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="0a207-236">Seleccione la opción **Rama predeterminada de la definición de compilación**.</span><span class="sxs-lookup"><span data-stu-id="0a207-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="0a207-237">Este filtro hace que la versión se desencadene solo para una compilación de la rama *master* del repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="0a207-238">Haga clic en el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-238">Click the **Save** button.</span></span> <span data-ttu-id="0a207-239">Haga clic en el botón **Aceptar** del cuadro de diálogo modal **Guardar** resultante.</span><span class="sxs-lookup"><span data-stu-id="0a207-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="0a207-240">Haga clic en el cuadro **Environment 1**.</span><span class="sxs-lookup"><span data-stu-id="0a207-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="0a207-241">Aparece un panel **Entorno** a la derecha.</span><span class="sxs-lookup"><span data-stu-id="0a207-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="0a207-242">Cambie el texto de *Environment 1* en el cuadro de texto **Nombre del entorno** a *Production*.</span><span class="sxs-lookup"><span data-stu-id="0a207-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Canalización de versión - cuadro de texto Nombre del entorno](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="0a207-244">Haga clic en el vínculo **1 fase, 2 tareas** del cuadro **Production**:</span><span class="sxs-lookup"><span data-stu-id="0a207-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Canalización de versión - Production environment link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="0a207-246">Aparece la pestaña **Tareas** del entorno.</span><span class="sxs-lookup"><span data-stu-id="0a207-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="0a207-247">Haga clic en la tarea **Deploy Azure App Service to Slot** (Implementar Azure App Service en espacio).</span><span class="sxs-lookup"><span data-stu-id="0a207-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="0a207-248">Su configuración aparece en un panel a la derecha.</span><span class="sxs-lookup"><span data-stu-id="0a207-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="0a207-249">Seleccione la suscripción de Azure asociada a la instancia de App Service en la lista desplegable **Suscripción de Azure**.</span><span class="sxs-lookup"><span data-stu-id="0a207-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="0a207-250">Una vez seleccionada, haga clic en el botón **Autorizar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="0a207-251">Seleccione *Aplicación web* en la lista desplegable **Tipo de aplicación**.</span><span class="sxs-lookup"><span data-stu-id="0a207-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="0a207-252">Seleccione *mywebapp/<número_único/>* en la lista desplegable **Nombre de App Service**.</span><span class="sxs-lookup"><span data-stu-id="0a207-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="0a207-253">Seleccione *AzureTutorial* en la lista desplegable **Grupo de recursos**.</span><span class="sxs-lookup"><span data-stu-id="0a207-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="0a207-254">Seleccione *staging* en la lista desplegable **Espacio**.</span><span class="sxs-lookup"><span data-stu-id="0a207-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="0a207-255">Haga clic en el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="0a207-256">Mantenga el mouse sobre el nombre de la canalización de versión predeterminada.</span><span class="sxs-lookup"><span data-stu-id="0a207-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="0a207-257">Haga clic en el icono de lápiz para editarlo.</span><span class="sxs-lookup"><span data-stu-id="0a207-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="0a207-258">Use *MyFirstProject-ASP.NET Core-CD* como nombre.</span><span class="sxs-lookup"><span data-stu-id="0a207-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nombre de la canalización de versión](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="0a207-260">Haga clic en el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="0a207-261">Confirmar cambios en GitHub e implementarlos automáticamente en Azure</span><span class="sxs-lookup"><span data-stu-id="0a207-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="0a207-262">Abra *SimpleFeedReader.sln* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a207-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="0a207-263">En el Explorador de soluciones, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0a207-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="0a207-264">Cambio de `<h2>Simple Feed Reader - V3</h2>` a `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="0a207-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="0a207-265">Presione **Ctrl**+**Mayús**+**B** para compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a207-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="0a207-266">Confirme el archivo en el repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="0a207-267">Use la página **Cambios** de la pestaña *Team Explorer* de Visual Studio, o bien ejecute lo siguiente con el shell de comandos del equipo local:</span><span class="sxs-lookup"><span data-stu-id="0a207-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="0a207-268">Inserte el cambio en la rama *master* del elemento *origin* remoto del repositorio de GitHub:</span><span class="sxs-lookup"><span data-stu-id="0a207-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="0a207-269">La confirmación aparece en la rama *master* del repositorio de GitHub:</span><span class="sxs-lookup"><span data-stu-id="0a207-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Confirmación de GitHub en la rama master](media/cicd/github-commit.png)

    <span data-ttu-id="0a207-271">La compilación se desencadena, ya que la integración continua está habilitada en la pestaña **Desencadenadores** de la definición de compilación:</span><span class="sxs-lookup"><span data-stu-id="0a207-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Habilitación de la integración continua](media/cicd/enable-ci.png)

1. <span data-ttu-id="0a207-273">Vaya a la pestaña **En cola** de la página **Azure Pipelines** > **Compilaciones** de Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="0a207-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="0a207-274">La compilación en cola muestra la rama y la confirmación que ha desencadenado la compilación:</span><span class="sxs-lookup"><span data-stu-id="0a207-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![Compilación en cola](media/cicd/build-queued.png)

1. <span data-ttu-id="0a207-276">Una vez que la compilación se realiza correctamente, se produce una implementación en Azure.</span><span class="sxs-lookup"><span data-stu-id="0a207-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="0a207-277">Vaya a la aplicación en el explorador.</span><span class="sxs-lookup"><span data-stu-id="0a207-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="0a207-278">Observe que el texto "V4" aparece en el encabezado:</span><span class="sxs-lookup"><span data-stu-id="0a207-278">Notice that the "V4" text appears in the heading:</span></span>

    ![Aplicación actualizada](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="0a207-280">Examinar la canalización de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="0a207-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="0a207-281">Definición de compilación</span><span class="sxs-lookup"><span data-stu-id="0a207-281">Build definition</span></span>

<span data-ttu-id="0a207-282">Se ha creado una definición de compilación con el nombre *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="0a207-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="0a207-283">Al finalizar, la compilación genera un archivo *.zip* que incluye los recursos que se van a publicar.</span><span class="sxs-lookup"><span data-stu-id="0a207-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="0a207-284">La canalización de versión implementa esos recursos en Azure.</span><span class="sxs-lookup"><span data-stu-id="0a207-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="0a207-285">La pestaña **Tareas** de la definición de compilación muestra los pasos individuales que se están usando.</span><span class="sxs-lookup"><span data-stu-id="0a207-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="0a207-286">Hay cinco tareas de compilación.</span><span class="sxs-lookup"><span data-stu-id="0a207-286">There are five build tasks.</span></span>

![Tareas de definición de compilación](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="0a207-288">**Restaurar** &mdash; Ejecuta el comando `dotnet restore` para restaurar los paquetes NuGet de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a207-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="0a207-289">La fuente de paquetes predeterminada empleada es nuget.org.</span><span class="sxs-lookup"><span data-stu-id="0a207-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="0a207-290">**Compilar** &mdash; Ejecuta el comando `dotnet build --configuration release` para compilar el código de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a207-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="0a207-291">Esta opción `--configuration` se usa para generar una versión optimizada del código adecuada para la implementación en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="0a207-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="0a207-292">Modifique la variable *BuildConfiguration* en la pestaña **Variables** de la definición de compilación si, por ejemplo, se necesita una configuración de depuración.</span><span class="sxs-lookup"><span data-stu-id="0a207-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="0a207-293">**Probar** &mdash; Ejecuta el comando `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` para ejecutar las pruebas unitarias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a207-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="0a207-294">Las pruebas unitarias se ejecutan en cualquier proyecto de C# que coincida con el patrón global `**/*Tests/*.csproj`.</span><span class="sxs-lookup"><span data-stu-id="0a207-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="0a207-295">Los resultados de las pruebas se guardan en un archivo *.trx* en la ubicación especificada por la opción `--results-directory`.</span><span class="sxs-lookup"><span data-stu-id="0a207-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="0a207-296">Si se produce un error en alguna prueba, ocurre lo mismo en la compilación, que no se implementa.</span><span class="sxs-lookup"><span data-stu-id="0a207-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="0a207-297">Para comprobar que las pruebas unitarias funcionan, modifique *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* para interrumpir una de las pruebas a propósito.</span><span class="sxs-lookup"><span data-stu-id="0a207-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="0a207-298">Por ejemplo, cambie `Assert.True(result.Count > 0);` por `Assert.False(result.Count > 0);` en el método `Returns_News_Stories_Given_Valid_Uri`.</span><span class="sxs-lookup"><span data-stu-id="0a207-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="0a207-299">Confirme e inserte el cambio en GitHub.</span><span class="sxs-lookup"><span data-stu-id="0a207-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="0a207-300">La compilación se desencadena y se produce un error.</span><span class="sxs-lookup"><span data-stu-id="0a207-300">The build is triggered and fails.</span></span> <span data-ttu-id="0a207-301">El estado de la canalización de compilación cambia a **error**.</span><span class="sxs-lookup"><span data-stu-id="0a207-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="0a207-302">Revierta el cambio, confirme e inserte de nuevo.</span><span class="sxs-lookup"><span data-stu-id="0a207-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="0a207-303">La compilación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="0a207-303">The build succeeds.</span></span>

1. <span data-ttu-id="0a207-304">**Publicar** &mdash; Ejecuta el comando `dotnet publish --configuration release --output <local_path_on_build_agent>` para generar un archivo *.zip* con los artefactos que se van a implementar.</span><span class="sxs-lookup"><span data-stu-id="0a207-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="0a207-305">La opción `--output` especifica la ubicación de publicación del archivo *.zip*.</span><span class="sxs-lookup"><span data-stu-id="0a207-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="0a207-306">Esa ubicación se especifica al pasar una [variable predefinida](/azure/devops/pipelines/build/variables) denominada `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="0a207-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="0a207-307">Esa variable se expande a una ruta de acceso local, como *c:\agent\_work\1\a*, en el agente de compilación.</span><span class="sxs-lookup"><span data-stu-id="0a207-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="0a207-308">**Publicar artefacto** &mdash; Publica el archivo *.zip* generado por la tarea **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="0a207-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="0a207-309">La tarea acepta la ubicación del archivo *.zip* como parámetro, que es la variable predefinida `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="0a207-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="0a207-310">El archivo *.zip* se publica como una carpeta denominada *drop*.</span><span class="sxs-lookup"><span data-stu-id="0a207-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="0a207-311">Haga clic en el vínculo **Resumen** de la definición de compilación para ver un historial de compilaciones con la definición:</span><span class="sxs-lookup"><span data-stu-id="0a207-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Captura de pantalla que muestra el historial de definiciones de compilación](media/cicd/build-definition-summary.png)

<span data-ttu-id="0a207-313">En la página resultante, haga clic en el vínculo correspondiente al número de compilación único:</span><span class="sxs-lookup"><span data-stu-id="0a207-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Captura de pantalla que muestra la página de resumen de definiciones de compilación](media/cicd/build-definition-completed.png)

<span data-ttu-id="0a207-315">Se muestra un resumen de esta compilación concreta.</span><span class="sxs-lookup"><span data-stu-id="0a207-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="0a207-316">Haga clic en la pestaña **Artefactos** y observe que se muestra la carpeta *drop* generada por la compilación:</span><span class="sxs-lookup"><span data-stu-id="0a207-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Captura de pantalla que muestra los artefactos de la definición de compilación - carpeta drop](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="0a207-318">Use los vínculos **Descargar** y **Explorar** para inspeccionar los artefactos publicados.</span><span class="sxs-lookup"><span data-stu-id="0a207-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="0a207-319">Canalización de versión</span><span class="sxs-lookup"><span data-stu-id="0a207-319">Release pipeline</span></span>

<span data-ttu-id="0a207-320">Se ha creado una canalización de versión con el nombre *MyFirstProject-ASP.NET Core-CI*:</span><span class="sxs-lookup"><span data-stu-id="0a207-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Captura de pantalla que muestra información general de la canalización de versión](media/cicd/release-definition-overview.png)

<span data-ttu-id="0a207-322">Los dos componentes principales de la canalización de versión son los **Artefactos** y los **Entornos**.</span><span class="sxs-lookup"><span data-stu-id="0a207-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="0a207-323">Al hacer clic en el cuadro de la sección **Artefactos**, se revela el siguiente panel:</span><span class="sxs-lookup"><span data-stu-id="0a207-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Captura de pantalla que muestra los artefactos de la canalización de versión](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="0a207-325">El valor **Origen (definición de compilación)** representa la definición de compilación a la que está vinculada esta canalización de versión.</span><span class="sxs-lookup"><span data-stu-id="0a207-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="0a207-326">El archivo *.zip* generado por una ejecución correcta de la definición de compilación se proporciona al entorno *Production* para su implementación en Azure.</span><span class="sxs-lookup"><span data-stu-id="0a207-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="0a207-327">Haga clic en el vínculo *1 fase, 2 tareas* del cuadro del entorno *Production* para ver las tareas de canalización de versión:</span><span class="sxs-lookup"><span data-stu-id="0a207-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Captura de pantalla que muestra las tareas de la canalización de versión](media/cicd/release-definition-tasks.png)

<span data-ttu-id="0a207-329">La canalización de versión consta de dos tareas: *Deploy Azure App Service to Slot* (Implementar Azure App Service en espacio) y *Manage Azure App Service - Slot Swap* (Administrar Azure App Service: intercambio de espacio).</span><span class="sxs-lookup"><span data-stu-id="0a207-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="0a207-330">Al hacer clic en la primera tarea, se revela la siguiente configuración de tarea:</span><span class="sxs-lookup"><span data-stu-id="0a207-330">Clicking the first task reveals the following task configuration:</span></span>

![Captura de pantalla que muestra la tarea de implementación de la canalización de versión](media/cicd/release-definition-task1.png)

<span data-ttu-id="0a207-332">La suscripción de Azure, el tipo de servicio, el nombre de la aplicación web, el grupo de recursos y el espacio de implementación se definen en la tarea de implementación.</span><span class="sxs-lookup"><span data-stu-id="0a207-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="0a207-333">El cuadro de texto **Paquete o carpeta** contiene la ruta de acceso del archivo *.zip* que se va a extraer e implementar en el *espacio de ensayo* de la aplicación web *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="0a207-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="0a207-334">Al hacer clic en la tarea de intercambio de espacio, se revela la siguiente configuración de tarea:</span><span class="sxs-lookup"><span data-stu-id="0a207-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Captura de pantalla que muestra la tarea de intercambio de espacio de la canalización de versión](media/cicd/release-definition-task2.png)

<span data-ttu-id="0a207-336">Se proporcionan los detalles de la suscripción, el grupo de recursos, el tipo de servicio, el nombre de la aplicación web y el espacio de implementación.</span><span class="sxs-lookup"><span data-stu-id="0a207-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="0a207-337">La casilla **Intercambiar con producción** está activada.</span><span class="sxs-lookup"><span data-stu-id="0a207-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="0a207-338">Por lo tanto, los bits implementados en el *espacio de ensayo* se intercambian en el entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="0a207-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="0a207-339">Lecturas adicionales</span><span class="sxs-lookup"><span data-stu-id="0a207-339">Additional reading</span></span>

* [<span data-ttu-id="0a207-340">Creación de la primera canalización con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="0a207-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="0a207-341">Compilación y proyecto de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0a207-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="0a207-342">Implementación de una aplicación web con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="0a207-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
