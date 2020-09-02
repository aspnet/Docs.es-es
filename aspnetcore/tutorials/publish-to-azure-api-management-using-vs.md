---
title: Publicación de una API web de ASP.NET Core en Azure API Management con Visual Studio
author: codemillmatt
description: Obtenga información sobre cómo publicar una API web de ASP.NET Core en Azure API Management con Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945579"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="e3896-103">Publicación de una API web de ASP.NET Core en Azure API Management con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3896-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="e3896-104">Por [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="e3896-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="e3896-105">Configurar</span><span class="sxs-lookup"><span data-stu-id="e3896-105">Set up</span></span>

- <span data-ttu-id="e3896-106">Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una.</span><span class="sxs-lookup"><span data-stu-id="e3896-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="e3896-107">[Cree una nueva instancia de Azure API Management](/azure/api-management/get-started-create-service-instance) si todavía no lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="e3896-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="e3896-108">[Cree un proyecto de aplicación de API web](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="e3896-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="e3896-109">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="e3896-109">Configure the app</span></span>

<span data-ttu-id="e3896-110">Agregar definiciones de Swagger a la API web de ASP.NET Core permite que Azure API Management lea las definiciones de API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e3896-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="e3896-111">Complete los siguientes pasos.</span><span class="sxs-lookup"><span data-stu-id="e3896-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="e3896-112">Incorporación de Swagger</span><span class="sxs-lookup"><span data-stu-id="e3896-112">Add Swagger</span></span>

1. <span data-ttu-id="e3896-113">Agregue el paquete NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) al proyecto de API web de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e3896-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Captura de pantalla para configurar el cuadro de diálogo NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="e3896-115">Agregue la siguiente línea al método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e3896-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="e3896-116">Agregue la siguiente línea al método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e3896-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="e3896-117">Cambio del enrutamiento de la API</span><span class="sxs-lookup"><span data-stu-id="e3896-117">Change the API routing</span></span>

<span data-ttu-id="e3896-118">Después, cambiará la estructura de la dirección URL necesaria para acceder a la acción `Get` de `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="e3896-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="e3896-119">Siga los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e3896-119">Complete the following steps:</span></span>

1. <span data-ttu-id="e3896-120">Abra el archivo *WeatherForecastController.cs*.</span><span class="sxs-lookup"><span data-stu-id="e3896-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="e3896-121">Elimine el atributo de nivel de clase `[Route("[controller]")]`.</span><span class="sxs-lookup"><span data-stu-id="e3896-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="e3896-122">La definición de clase tendrá un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e3896-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="e3896-123">Agregue un atributo `[Route("/")]` a la acción `Get()`.</span><span class="sxs-lookup"><span data-stu-id="e3896-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="e3896-124">La definición de función tendrá un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e3896-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="e3896-125">Publicación de la API web en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e3896-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="e3896-126">Complete los pasos siguientes para publicar la API web de ASP.NET Core en Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="e3896-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="e3896-127">Publique la aplicación de API en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e3896-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="e3896-128">Agregue una API en blanco a la instancia de servicio de Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="e3896-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="e3896-129">Publique la aplicación de API web de ASP.NET Core en la instancia de servicio de Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="e3896-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="e3896-130">Publicación de la aplicación de API en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e3896-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="e3896-131">Complete los pasos siguientes para publicar la API web de ASP.NET Core en Azure API Management:</span><span class="sxs-lookup"><span data-stu-id="e3896-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="e3896-132">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="e3896-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="e3896-134">En el cuadro de diálogo **Publicar**, seleccione **Azure** y el botón **Siguiente**:</span><span class="sxs-lookup"><span data-stu-id="e3896-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Cuadro de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="e3896-136">Seleccione **Azure App Service (Windows)** y el botón **Siguiente**:</span><span class="sxs-lookup"><span data-stu-id="e3896-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Cuadro de diálogo Publicar: selección de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="e3896-138">Seleccione **Create a new Azure App Service** (Crear una instancia de Azure App Service).</span><span class="sxs-lookup"><span data-stu-id="e3896-138">Select **Create a new Azure App Service**.</span></span>

    ![Cuadro de diálogo Publicar: selección de una instancia de servicio de Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="e3896-140">Se muestra el cuadro de diálogo **Create App Service** (Crear una instancia de App Service).</span><span class="sxs-lookup"><span data-stu-id="e3896-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="e3896-141">Se rellenan los campos de entrada **Nombre de la aplicación**, **Grupo de recursos** y **Plan de App Service**.</span><span class="sxs-lookup"><span data-stu-id="e3896-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="e3896-142">Puede mantener estos nombres o cambiarlos.</span><span class="sxs-lookup"><span data-stu-id="e3896-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="e3896-143">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e3896-143">Select the **Create** button.</span></span>

    ![Cuadro de diálogo Crear servicio de aplicaciones](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="e3896-145">Una vez terminada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro **Publicar** vuelve a obtener el foco.</span><span class="sxs-lookup"><span data-stu-id="e3896-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="e3896-146">La instancia que se ha creado se selecciona automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e3896-146">The instance that was created is automatically selected.</span></span>

![Cuadro de diálogo Publicar: selección de la instancia de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="e3896-148">En este punto, debe agregar una API al servicio Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="e3896-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="e3896-149">Deje Visual Studio abierto mientras completa las tareas siguientes.</span><span class="sxs-lookup"><span data-stu-id="e3896-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="e3896-150">Incorporación de una API a Azure API Management</span><span class="sxs-lookup"><span data-stu-id="e3896-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="e3896-151">Abra la instancia de servicio de API Management que se ha creado anteriormente en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e3896-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="e3896-152">Seleccione la hoja **API**:</span><span class="sxs-lookup"><span data-stu-id="e3896-152">Select the **APIs** blade:</span></span>

    ![Hojas API seleccionadas de la instancia de servicio de API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="e3896-154">En el panel de **Add a new API** (Agregar una nueva API), seleccione el icono **API en blanco**:</span><span class="sxs-lookup"><span data-stu-id="e3896-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![Pantalla que muestra el icono de API en blanco resaltado](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="e3896-156">Escriba los valores siguientes en el cuadro de diálogo **Crear una API en blanco** que aparece:</span><span class="sxs-lookup"><span data-stu-id="e3896-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="e3896-157">**Nombre para mostrar**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="e3896-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="e3896-158">**Nombre**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="e3896-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="e3896-159">**Sufijo de la URL de API**: *v1*</span><span class="sxs-lookup"><span data-stu-id="e3896-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="e3896-160">Deje en blanco el campo **Dirección URL del servicio web**.</span><span class="sxs-lookup"><span data-stu-id="e3896-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="e3896-161">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e3896-161">Select the **Create** button.</span></span>

    ![Captura de pantalla del cuadro de diálogo Crear una API en blanco completada](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="e3896-163">Se crea la API en blanco.</span><span class="sxs-lookup"><span data-stu-id="e3896-163">The blank API is created.</span></span>

![Captura de pantalla de una API en blanco en el portal de API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="e3896-165">Publicación de la API web de ASP.NET Core en Azure API Management</span><span class="sxs-lookup"><span data-stu-id="e3896-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="e3896-166">Vuelva a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e3896-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="e3896-167">El cuadro de diálogo **Publicar** debe estar abierto en el punto en que se ha quedado antes.</span><span class="sxs-lookup"><span data-stu-id="e3896-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="e3896-168">Seleccione el servicio de Azure App Service recientemente publicado para que esté resaltado.</span><span class="sxs-lookup"><span data-stu-id="e3896-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="e3896-169">Haga clic en el botón **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e3896-169">Select the **Next** button.</span></span>

    ![Captura de pantalla del cuadro de diálogo Publicar con el servicio de aplicaciones seleccionado](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="e3896-171">En el cuadro de diálogo se muestra ahora el servicio de Azure API Management que se ha creado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e3896-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="e3896-172">Expanda eso y la carpeta *API* para ver la API en blanco que se ha creado.</span><span class="sxs-lookup"><span data-stu-id="e3896-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="e3896-173">Seleccione el nombre de la API en blanco y el botón **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="e3896-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![Captura de pantalla de la API en blanco recientemente creada de Azure API Management seleccionada en el cuadro de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="e3896-175">El cuadro de diálogo se cierra y aparece una pantalla de resumen con información sobre la publicación.</span><span class="sxs-lookup"><span data-stu-id="e3896-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="e3896-176">Seleccione el botón **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="e3896-176">Select the **Publish** button.</span></span>

    ![Captura de pantalla de Visual Studio con el perfil de publicación mostrado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="e3896-178">La API web se publicará tanto en Azure App Service como en Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="e3896-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="e3896-179">Aparecerá una nueva ventana del explorador y mostrará la API en ejecución en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e3896-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="e3896-180">Puede cerrar esa ventana.</span><span class="sxs-lookup"><span data-stu-id="e3896-180">You can close that window.</span></span>

1. <span data-ttu-id="e3896-181">Vuelva a la instancia de Azure API Management en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e3896-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="e3896-182">Actualice la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="e3896-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="e3896-183">Seleccione la API en blanco que se ha creado en los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="e3896-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="e3896-184">Ahora está rellenada y puede explorarla.</span><span class="sxs-lookup"><span data-stu-id="e3896-184">It's now populated and you can explore around.</span></span>

    ![Captura de pantalla de la API rellenada en Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="e3896-186">Configuración del nombre de la API publicada</span><span class="sxs-lookup"><span data-stu-id="e3896-186">Configure the published API name</span></span>

<span data-ttu-id="e3896-187">Tenga en cuenta que el nombre de la API es diferente que cuando se le ha asignado un nombre.</span><span class="sxs-lookup"><span data-stu-id="e3896-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="e3896-188">La API publicada se denomina *WeatherAPI*; sin embargo, al crearla se le ha asignado el nombre *WeatherForecasts*.</span><span class="sxs-lookup"><span data-stu-id="e3896-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="e3896-189">Complete los pasos siguientes para corregir el nombre:</span><span class="sxs-lookup"><span data-stu-id="e3896-189">Complete the following steps to fix the name:</span></span>

![Captura de pantalla de la API rellenada con nombres diferentes resaltados](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="e3896-191">Elimine la siguiente línea del método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e3896-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="e3896-192">Agregue el código siguiente al método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e3896-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="e3896-193">Abra el perfil de publicación que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="e3896-193">Open the newly created publish profile.</span></span> <span data-ttu-id="e3896-194">Se puede encontrar en la carpeta *Properties/PublishProfiles* del **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="e3896-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Captura de pantalla en la que se muestra la ubicación del archivo perfil de publicación resaltado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="e3896-196">Cambie el valor del elemento `<OpenAPIDocumentName>`, de `v1` a `WeatherForecasts`.</span><span class="sxs-lookup"><span data-stu-id="e3896-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![Captura de pantalla de los cambios necesarios para el perfil de publicación](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="e3896-198">Vuelva a publicar la API web de ASP.NET Core y abra la instancia de Azure API Management en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e3896-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="e3896-199">Actualice la página en el explorador.</span><span class="sxs-lookup"><span data-stu-id="e3896-199">Refresh the page in your browser.</span></span> <span data-ttu-id="e3896-200">Verá que el nombre de la API ahora es correcto.</span><span class="sxs-lookup"><span data-stu-id="e3896-200">You'll see the name of the API is now correct.</span></span>

    ![Captura de pantalla de la API completada en el portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="e3896-202">Comprobación del funcionamiento de la API web</span><span class="sxs-lookup"><span data-stu-id="e3896-202">Verify the web API is working</span></span>

<span data-ttu-id="e3896-203">Puede probar la API web de ASP.NET Core implementada en Azure API Management desde Azure Portal con los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e3896-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="e3896-204">Abra la pestaña **Prueba**.</span><span class="sxs-lookup"><span data-stu-id="e3896-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="e3896-205">Seleccione **/** o la operación **Get**.</span><span class="sxs-lookup"><span data-stu-id="e3896-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="e3896-206">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e3896-206">Select **Send**.</span></span>

    ![Captura de pantalla del portal antes de la prueba](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="e3896-208">Una respuesta correcta tendrá un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e3896-208">A successful response will look like the following:</span></span>

![Captura de pantalla de una respuesta correcta de API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="e3896-210">Limpieza</span><span class="sxs-lookup"><span data-stu-id="e3896-210">Clean up</span></span>

<span data-ttu-id="e3896-211">Cuando haya terminado de probar la aplicación, vaya a [Azure Portal](https://portal.azure.com/) y elimínela.</span><span class="sxs-lookup"><span data-stu-id="e3896-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="e3896-212">Seleccione **Grupos de recursos** y, luego, el grupo de recursos que haya creado.</span><span class="sxs-lookup"><span data-stu-id="e3896-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure Portal: Grupos de recursos en el menú lateral](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="e3896-214">En la página **Grupos de recursos**, seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="e3896-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure Portal: página Grupos de recursos](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="e3896-216">Escriba el nombre del grupo de recursos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="e3896-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="e3896-217">La aplicación y todos los demás recursos que ha creado en este tutorial se han eliminado de Azure.</span><span class="sxs-lookup"><span data-stu-id="e3896-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e3896-218">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="e3896-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="e3896-219">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e3896-219">Additional resources</span></span>

- [<span data-ttu-id="e3896-220">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="e3896-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="e3896-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e3896-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
