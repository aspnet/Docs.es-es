---
title: Publicar una aplicación de ASP.NET Core en Azure con Visual Studio
author: rick-anderson
description: Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 1fced12700fcd5910c1484ebb9190c7652b2646e
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130709"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="f8d4b-103">Publicar una aplicación de ASP.NET Core en Azure con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8d4b-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="f8d4b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8d4b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="f8d4b-105">Consulte [Publicación de una aplicación web en Azure App Service mediante Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) si trabaja en macOS.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="f8d4b-106">Para solucionar un problema de implementación de App Service, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="f8d4b-107">Configurar</span><span class="sxs-lookup"><span data-stu-id="f8d4b-107">Set up</span></span>

* <span data-ttu-id="f8d4b-108">Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="f8d4b-109">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="f8d4b-109">Create a web app</span></span>

<span data-ttu-id="f8d4b-110">En la página de inicio de Visual Studio, seleccione **Archivo > Nuevo > Proyecto...** .</span><span class="sxs-lookup"><span data-stu-id="f8d4b-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![menú Archivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="f8d4b-112">Complete el cuadro de diálogo **Nuevo proyecto**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="f8d4b-113">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f8d4b-114">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-114">Select **Next**.</span></span>

![Cuadro de diálogo Nuevo proyecto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="f8d4b-116">En el cuadro de diálogo **Nueva aplicación web de ASP.NET Core**, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="f8d4b-117">Seleccione **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-117">Select **Web Application**.</span></span>
* <span data-ttu-id="f8d4b-118">En Autenticación, seleccione **Cambiar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-118">Select **Change** under Authentication.</span></span>

![Cuadro de diálogo Nueva aplicación web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="f8d4b-120">Se mostrará el cuadro de diálogo **Cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="f8d4b-121">Seleccione **Cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="f8d4b-122">Seleccione **Aceptar** para volver a **Nueva aplicación web ASP.NET Core** y, luego, elija **Crear**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Cuadro de diálogo de autenticación web de ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="f8d4b-124">Visual Studio crea la solución.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="f8d4b-125">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="f8d4b-125">Run the app</span></span>

* <span data-ttu-id="f8d4b-126">Presione CTRL+F5 para ejecutar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="f8d4b-127">Pruebe el vínculo **Privacidad**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-127">Test the **Privacy** link.</span></span>

![Aplicación web abierta en Microsoft Edge en host local](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="f8d4b-129">Registrar un usuario</span><span class="sxs-lookup"><span data-stu-id="f8d4b-129">Register a user</span></span>

* <span data-ttu-id="f8d4b-130">Seleccione **Registrar** y registre a un usuario nuevo.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="f8d4b-131">Puede usar una dirección de correo electrónico ficticia.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-131">You can use a fictitious email address.</span></span> <span data-ttu-id="f8d4b-132">Al enviar la información, la página mostrará el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="f8d4b-133">*"Error en una operación de base de datos al procesar la solicitud. La aplicación de las migraciones existentes para el contexto de base de datos de la aplicación puede solucionar este problema".*</span><span class="sxs-lookup"><span data-stu-id="f8d4b-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="f8d4b-134">Seleccione **Aplicar migraciones** y, una vez actualizada la página, vuelva a cargarla.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Error en una operación de base de datos al procesar la solicitud.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="f8d4b-137">La aplicación muestra el correo electrónico usado para registrar el nuevo usuario y un vínculo **Cerrar sesión**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Aplicación web abierta en Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="f8d4b-140">Implementación de la aplicación en Azure</span><span class="sxs-lookup"><span data-stu-id="f8d4b-140">Deploy the app to Azure</span></span>

<span data-ttu-id="f8d4b-141">Desde el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Publicar...**</span><span class="sxs-lookup"><span data-stu-id="f8d4b-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="f8d4b-143">En el cuadro de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="f8d4b-144">Seleccione **Azure**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-144">Select **Azure**.</span></span>
* <span data-ttu-id="f8d4b-145">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-145">Select **Next**.</span></span>

![Cuadro de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="f8d4b-147">En el cuadro de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="f8d4b-148">Seleccione **Azure App Service (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="f8d4b-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="f8d4b-149">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-149">Select **Next**.</span></span>

![Cuadro de diálogo Publicar: selección de un servicio de Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="f8d4b-151">En el cuadro de diálogo **Publicar**, seleccione **Create a new Azure App Service** (Crear una instancia de Azure App Service):</span><span class="sxs-lookup"><span data-stu-id="f8d4b-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Cuadro de diálogo Publicar: selección de una instancia de servicio de Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="f8d4b-153">Aparece el cuadro de diálogo **Crear servicio de aplicaciones**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="f8d4b-154">Se rellenan los campos de entrada **Nombre de la aplicación**, **Grupo de recursos** y **Plan de App Service**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="f8d4b-155">Puede mantener estos nombres o cambiarlos.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="f8d4b-156">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-156">Select **Create**.</span></span>

![Cuadro de diálogo Crear servicio de aplicaciones](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="f8d4b-158">Una vez terminada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro de diálogo **Publicar** vuelve a recibir el foco:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="f8d4b-159">La nueva instancia que se acaba de crear se selecciona automáticamente.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="f8d4b-160">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-160">Select **Finish**.</span></span>

![Cuadro de diálogo Publicar: selección de la instancia de App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="f8d4b-162">A continuación, verá la página de **resumen del perfil de publicación**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="f8d4b-163">Visual Studio ha detectado que esta aplicación requiere una base de datos de SQL Server y le pide que la configure.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="f8d4b-164">Seleccione **Configurar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-164">Select **Configure**.</span></span>

![Página de resumen del perfil de publicación: configuración de la dependencia de SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="f8d4b-166">Aparece el cuadro de diálogo **Configurar dependencia**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="f8d4b-167">Seleccione **Azure SQL Database**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="f8d4b-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-168">Select **Next**.</span></span>

![Cuadro de diálogo de configuración de dependencia de SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="f8d4b-170">En el cuadro de diálogo **Configure Azure SQL database** (Configurar base de datos de Azure SQL), seleccione **Crear una base de datos SQL**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Cuadro de diálogo de configuración de una base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="f8d4b-172">Aparece el cuadro de diálogo **Crear una base de datos de Azure SQL**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="f8d4b-173">Se rellenan los campos de entrada de datos **Nombre de la base de datos**, **Grupo de recursos**, **Servidor de base de datos** y **Plan de App Service**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="f8d4b-174">Puede mantener estos nombres o cambiarlos.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="f8d4b-175">Especifique los valores de **Database administrator username** (Nombre de usuario del administrador de la base de datos) y **Database administrator password** (Contraseña del administrador de la base de datos) del **servidor de bases de datos** seleccionado (tenga en cuenta que la cuenta que use debe tener los permisos necesarios para crear la base de datos de Azure SQL).</span><span class="sxs-lookup"><span data-stu-id="f8d4b-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="f8d4b-176">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-176">Select **Create**.</span></span>

![Cuadro de diálogo de nueva base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="f8d4b-178">Una vez finalizada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro de diálogo **Configure Azure SQL Database** (Configurar base de datos de Azure SQL), vuelve a recibir el foco:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="f8d4b-179">La nueva instancia que se acaba de crear se selecciona automáticamente.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="f8d4b-180">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-180">Select **Next**.</span></span>

![Cuadro de diálogo de configuración de una base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="f8d4b-182">En el paso siguiente del cuadro de diálogo **Configure Azure SQL Database** (Configurar base de datos de Azure SQL):</span><span class="sxs-lookup"><span data-stu-id="f8d4b-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="f8d4b-183">Rellene los campos **Database connection user name** (Nombre de usuario de conexión de la base de datos) y **Database connection password** (Contraseña de conexión de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="f8d4b-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="f8d4b-184">Estos son los detalles que utilizará la aplicación para conectarse a la base de datos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="f8d4b-185">El procedimiento recomendado es evitar el uso de los mismos detalles que el nombre de usuario y la contraseña de administrador usados en el paso anterior.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="f8d4b-186">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-186">Select **Finish**.</span></span>

![Cuadro de diálogo de configuración de una base de datos de Azure SQL, detalles de la cadena de conexión](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="f8d4b-188">En la página de **resumen del perfil de publicación**, seleccione **Configuración**:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![Página de resumen del perfil de publicación: edición de la configuración](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="f8d4b-190">En la página **Configuración** del cuadro de diálogo **Publicar**, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8d4b-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="f8d4b-191">Expanda **Bases de datos** y active **Usar esta cadena de conexión en tiempo de ejecución**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="f8d4b-192">Expanda **Migraciones de Entity Framework** y active **Aplicar esta migración al publicar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="f8d4b-193">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-193">Select **Save**.</span></span> <span data-ttu-id="f8d4b-194">Visual Studio volverá al cuadro de diálogo **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Cuadro de diálogo Publicar: Panel Configuración](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="f8d4b-196">Haga clic en **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-196">Click **Publish**.</span></span> <span data-ttu-id="f8d4b-197">Visual Studio publica la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="f8d4b-198">Cuando la implementación termine, la aplicación se abre en un explorador.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-198">When the deployment completes, the app is opened in a browser.</span></span>

![Cuadro de diálogo Publicar: Panel Configuración](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="f8d4b-200">Actualización de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f8d4b-200">Update the app</span></span>

* <span data-ttu-id="f8d4b-201">Edite la página de Razor *Pages/Index.cshtml* y cambie su contenido.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="f8d4b-202">Por ejemplo, puede editar el párrafo para que incluya el mensaje "¡Hola, ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="f8d4b-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="f8d4b-203">Seleccione **Publicar** de nuevo en la página de **resumen del perfil de publicación**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Página de resumen del perfil de publicación](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="f8d4b-205">Una vez publicada la aplicación, compruebe que los cambios realizados estén disponibles en Azure.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Comprobar si se ha completado la tarea](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="f8d4b-207">Limpieza</span><span class="sxs-lookup"><span data-stu-id="f8d4b-207">Clean up</span></span>

<span data-ttu-id="f8d4b-208">Cuando haya terminado de probar la aplicación, vaya a [Azure Portal](https://portal.azure.com/) y elimínela.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="f8d4b-209">Seleccione **Grupos de recursos** y, luego, el grupo de recursos que haya creado.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: Grupos de recursos en el menú lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="f8d4b-211">En la página **Grupos de recursos**, seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: página Grupos de recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="f8d4b-213">Escriba el nombre del grupo de recursos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="f8d4b-214">La aplicación y todos los demás recursos que ha creado en este tutorial se han eliminado de Azure.</span><span class="sxs-lookup"><span data-stu-id="f8d4b-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="f8d4b-215">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f8d4b-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="f8d4b-216">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f8d4b-216">Additional resources</span></span>

* <span data-ttu-id="f8d4b-217">Para Visual Studio Code, vea [Publicar los perfiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="f8d4b-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="f8d4b-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f8d4b-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="f8d4b-219">Grupos de recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="f8d4b-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="f8d4b-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="f8d4b-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
