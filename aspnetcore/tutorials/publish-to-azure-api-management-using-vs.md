---
title: Publicación de una API web de ASP.NET Core en Azure API Management con Visual Studio
author: codemillmatt
description: Obtenga información sobre cómo publicar una API web de ASP.NET Core en Azure API Management con Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "96332244"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Publicación de una API web de ASP.NET Core en Azure API Management con Visual Studio

Por [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Configurar

- Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una.
- [Cree una nueva instancia de Azure API Management](/azure/api-management/get-started-create-service-instance) si todavía no lo ha hecho.
- [Cree un proyecto de aplicación de API web](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Configuración de la aplicación

Agregar definiciones de Swagger a la API web de ASP.NET Core permite que Azure API Management lea las definiciones de API de la aplicación. Complete los siguientes pasos.

### <a name="add-swagger"></a>Incorporación de Swagger

1. Agregue el paquete NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) al proyecto de API web de ASP.NET Core:

    ![Captura de pantalla para configurar el cuadro de diálogo NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Agregue la siguiente línea al método `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Agregue la siguiente línea al método `Startup.Configure`:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Cambio del enrutamiento de la API

Después, cambiará la estructura de la dirección URL necesaria para acceder a la acción `Get` de `WeatherForecastController`. Siga los pasos siguientes:

1. Abra el archivo *WeatherForecastController.cs*.
1. Elimine el atributo de nivel de clase `[Route("[controller]")]`. La definición de clase tendrá un aspecto similar al siguiente:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Agregue un atributo `[Route("/")]` a la acción `Get()`. La definición de función tendrá un aspecto similar al siguiente:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Publicación de la API web en Azure App Service

Complete los pasos siguientes para publicar la API web de ASP.NET Core en Azure API Management:

1. Publique la aplicación de API en Azure App Service.
1. Agregue una API en blanco a la instancia de servicio de Azure API Management.
1. Publique la aplicación de API web de ASP.NET Core en la instancia de servicio de Azure API Management.

### <a name="publish-the-api-app-to-azure-app-service"></a>Publicación de la aplicación de API en Azure App Service

Complete los pasos siguientes para publicar la API web de ASP.NET Core en Azure API Management:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**:

    ![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. En el cuadro de diálogo **Publicar**, seleccione **Azure** y el botón **Siguiente**:

    ![Cuadro de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Seleccione **Azure App Service (Windows)** y el botón **Siguiente**:

    ![Cuadro de diálogo Publicar: selección de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Seleccione **Create a new Azure App Service** (Crear una instancia de Azure App Service).

    ![Cuadro de diálogo Publicar: selección de una instancia de servicio de Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    Se muestra el cuadro de diálogo **Create App Service** (Crear una instancia de App Service). Se rellenan los campos de entrada **Nombre de la aplicación**, **Grupo de recursos** y **Plan de App Service**. Puede mantener estos nombres o cambiarlos.
1. Seleccione el botón **Crear**.

    ![Cuadro de diálogo Crear servicio de aplicaciones](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Una vez terminada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro **Publicar** vuelve a obtener el foco. La instancia que se ha creado se selecciona automáticamente.

![Cuadro de diálogo Publicar: selección de la instancia de App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

En este punto, debe agregar una API al servicio Azure API Management. Deje Visual Studio abierto mientras completa las tareas siguientes.

### <a name="add-an-api-to-azure-api-management"></a>Incorporación de una API a Azure API Management

1. Abra la instancia de servicio de API Management que se ha creado anteriormente en Azure Portal. Seleccione la hoja **API**:

  ![Hojas API seleccionadas de la instancia de servicio de API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Seleccione los tres puntos junto a **Echo API** y, a continuación, **Eliminar**, en el menú desplegable, para quitarla.

  ![Eliminación de Echo API de la instancia del servicio API Management](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. En el panel de **Add a new API** (Agregar una nueva API), seleccione el icono **API en blanco**:

  ![Pantalla que muestra el icono de API en blanco resaltado](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Escriba los valores siguientes en el cuadro de diálogo **Crear una API en blanco** que aparece:    

    - **Nombre para mostrar**: *WeatherForecasts*
    - **Nombre**: *WeatherForecasts*
    - **Sufijo de la URL de API**: *v1*
    - Deje en blanco el campo **Dirección URL del servicio web**.

1. Seleccione el botón **Crear**.

    ![Captura de pantalla del cuadro de diálogo Crear una API en blanco completada](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Se crea la API en blanco.

![Captura de pantalla de una API en blanco en el portal de API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Publicación de la API web de ASP.NET Core en Azure API Management

1. Vuelva a Visual Studio. El cuadro de diálogo **Publicar** debe estar abierto en el punto en que se ha quedado antes.
1. Seleccione el servicio de Azure App Service recientemente publicado para que esté resaltado.
1. Haga clic en el botón **Siguiente**.

    ![Captura de pantalla del cuadro de diálogo Publicar con el servicio de aplicaciones seleccionado](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. En el cuadro de diálogo se muestra ahora el servicio de Azure API Management que se ha creado anteriormente. Expanda eso y la carpeta *API* para ver la API en blanco que se ha creado.
1. Seleccione el nombre de la API en blanco y el botón **Finalizar**.

    ![Captura de pantalla de la API en blanco recientemente creada de Azure API Management seleccionada en el cuadro de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. El cuadro de diálogo se cierra y aparece una pantalla de resumen con información sobre la publicación. Seleccione el botón **Publicar**.

    ![Captura de pantalla de Visual Studio con el perfil de publicación mostrado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    La API web se publicará tanto en Azure App Service como en Azure API Management. Aparecerá una nueva ventana del explorador y mostrará la API en ejecución en Azure App Service. Puede cerrar esa ventana.

1. Vuelva a la instancia de Azure API Management en Azure Portal.
1. Actualice la ventana del explorador.
1. Seleccione la API en blanco que se ha creado en los pasos anteriores. Ahora está rellenada y puede explorarla.

    ![Captura de pantalla de la API rellenada en Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Configuración del nombre de la API publicada

Tenga en cuenta que el nombre de la API es diferente que cuando se le ha asignado un nombre. La API publicada se denomina *WeatherAPI*; sin embargo, al crearla se le ha asignado el nombre *WeatherForecasts*. Complete los pasos siguientes para corregir el nombre:

![Captura de pantalla de la API rellenada con nombres diferentes resaltados](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Elimine la siguiente línea del método `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Agregue el código siguiente al método `Startup.ConfigureServices`:
    
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

1. Abra el perfil de publicación que acaba de crear. Se puede encontrar en la carpeta *Properties/PublishProfiles* del **Explorador de soluciones**.

    ![Captura de pantalla en la que se muestra la ubicación del archivo perfil de publicación resaltado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Cambie el valor del elemento `<OpenAPIDocumentName>`, de `v1` a `WeatherForecasts`.

    ![Captura de pantalla de los cambios necesarios para el perfil de publicación](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Vuelva a publicar la API web de ASP.NET Core y abra la instancia de Azure API Management en Azure Portal.
1. Actualice la página en el explorador. Verá que el nombre de la API ahora es correcto.

    ![Captura de pantalla de la API completada en el portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Comprobación del funcionamiento de la API web

Puede probar la API web de ASP.NET Core implementada en Azure API Management desde Azure Portal con los pasos siguientes:

1. Abra la pestaña **Prueba**.
1. Seleccione **/** o la operación **Get**.
1. Seleccione **Enviar**.

    ![Captura de pantalla del portal antes de la prueba](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Una respuesta correcta tendrá un aspecto similar al siguiente:

![Captura de pantalla de una respuesta correcta de API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Limpieza

Cuando haya terminado de probar la aplicación, vaya a [Azure Portal](https://portal.azure.com/) y elimínela.

1. Seleccione **Grupos de recursos** y, luego, el grupo de recursos que haya creado.

    ![Azure Portal: Grupos de recursos en el menú lateral](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. En la página **Grupos de recursos**, seleccione **Eliminar**.

    ![Azure Portal: página Grupos de recursos](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Escriba el nombre del grupo de recursos y seleccione **Eliminar**. La aplicación y todos los demás recursos que ha creado en este tutorial se han eliminado de Azure.

## <a name="next-steps"></a>Pasos siguientes

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionales

- [Azure API Management](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
