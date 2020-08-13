---
title: 'Implementación de una aplicación en App Service: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Implemente una aplicación ASP.NET Core en Azure App Service, el primer paso para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f27c33d692512130ba0a36e9d00d46dffe90da16
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130241"
---
# <a name="deploy-an-app-to-app-service"></a>Implementación de una aplicación en App Service

[Azure App Service](/azure/app-service/) es la plataforma de hospedaje web de Azure. La implementación de una aplicación web en Azure App Service puede realizarse manualmente o mediante un proceso automatizado. En esta sección de la guía se describen los métodos de implementación que se pueden desencadenar manualmente o mediante un script con la línea de comandos o que se desencadenan manualmente con Visual Studio.

En esta sección, se realizarán las siguientes tareas:

* Descargar y compilar la aplicación de ejemplo.
* Crear una aplicación web de Azure App Service con Azure Cloud Shell.
* Implementar la aplicación de ejemplo en Azure con Git.
* Implementar un cambio en la aplicación mediante Visual Studio.
* Agregar un espacio de ensayo a la aplicación web.
* Implementar una actualización en el espacio de ensayo.
* Intercambiar los espacios de ensayo y producción.

## <a name="download-and-test-the-app"></a>Descargar y probar la aplicación

La aplicación que se usa en esta guía es una aplicación ASP.NET Core pregenerada, [lector de fuentes simple](https://github.com/Azure-Samples/simple-feed-reader/). Se trata de una aplicación de Razor Pages que usa la API `Microsoft.SyndicationFeed.ReaderWriter` para recuperar una fuente RSS/Atom y mostrar las noticias en una lista.

No dude en revisar el código, pero es importante entender que esta aplicación no tiene nada de extraordinario. Es simplemente una aplicación de ASP.NET Core con fines ilustrativos.

Desde un shell de comandos, descargue el código, compile el proyecto y ejecútelo como se indica a continuación.

> *Nota: Los usuarios de Linux/macOS deben realizar los cambios adecuados en las rutas de acceso, por ejemplo, mediante una barra diagonal (`/`), en lugar de la barra diagonal inversa (`\`).*

1. Clone el código en una carpeta del equipo local.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Cambie la carpeta de trabajo a la carpeta *simple-feed-reader* que se creó.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaure los paquetes y compile la solución.

    ```dotnetcli
    dotnet build
    ```

4. Ejecutar la aplicación.

    ```dotnetcli
    dotnet run
    ```

    ![El comando de ejecución de dotnet es correcto](./media/deploying-to-app-service/dotnet-run.png)

5. Abra un explorador y vaya a `http://localhost:5000`. La aplicación permite escribir o pegar una dirección URL de fuente de distribución y ver una lista de elementos de noticias.

     ![Aplicación que muestra el contenido de una fuente RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Una vez que esté satisfecho de que la aplicación funcione correctamente, ciérrela presionando **Ctrl**+**C** en el shell de comandos.

## <a name="create-the-azure-app-service-web-app"></a>Crear la aplicación web de Azure App Service

Para implementar la aplicación, debe crear una [aplicación web](/azure/app-service/app-service-web-overview) de App Service. Después de crear la aplicación web, debe implementarla en el equipo local con Git.

1. Inicie sesión en [Azure Cloud Shell](https://shell.azure.com/bash). Nota: Cuando inicie sesión por primera vez, Cloud Shell le pedirá que cree una cuenta de almacenamiento para los archivos de configuración. Acepte los valores predeterminados o proporcione un nombre único.

2. Use Cloud Shell para los pasos siguientes.

    a. Declare una variable para almacenar el nombre de la aplicación web. El nombre debe ser único para usarse en la dirección URL predeterminada. El uso de la función Bash `$RANDOM` para construir el nombre garantiza la exclusividad y tiene como resultado el formato `webappname99999`.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Cree un grupo de recursos. Los grupos de recursos proporcionan un medio para agregar recursos de Azure para administrarlos como un grupo.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    El comando `az` invoca la [CLI de Azure](/cli/azure/). La CLI se puede ejecutar localmente, pero su uso en Cloud Shell ahorra tiempo y configuración.

    c. Cree un plan de App Service en el nivel S1. Un plan de App Service es una agrupación de aplicaciones web que comparten el mismo plan de tarifa. El nivel S1 no es gratuito, pero es necesario para la característica de espacios de ensayo.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Cree el recurso de la aplicación web con el plan de App Service en el mismo grupo de recursos.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Cree las credenciales de implementación. Estas credenciales de implementación se aplican a todas las aplicaciones web de su suscripción. No utilice caracteres especiales en el nombre de usuario.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configure la aplicación web para que acepte implementaciones de Git local y muestre la *dirección URL de implementación de Git*. **Tenga en cuenta esta dirección URL como referencia más adelante**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Muestre la dirección *URL de la aplicación web*. Vaya a esta dirección URL para ver la aplicación web en blanco. **Tenga en cuenta esta dirección URL como referencia más adelante**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Con un shell de comandos en el equipo local, vaya a la carpeta de proyecto de la aplicación web (por ejemplo, `.\simple-feed-reader\SimpleFeedReader`). Ejecute los siguientes comandos para configurar Git a fin de que envíe cambios a la dirección URL de implementación:

    a. Agregue la dirección URL remota al repositorio local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Inserte la rama *maestra* local en la rama *maestra* de *azure-prod* remoto.

    ```console
    git push azure-prod master
    ```

    Se le pedirán las credenciales de implementación que creó anteriormente. Observe el resultado en el shell de comandos. Azure crea la aplicación ASP.NET Core de forma remota.

4. En un explorador, vaya a la *dirección URL de la aplicación web* y tenga en cuenta que la aplicación se ha compilado e implementado. Se pueden confirmar otros cambios en el repositorio local de Git con `git commit`. Estos cambios se insertan en Azure con el comando `git push` anterior.

## <a name="deployment-with-visual-studio"></a>Implementación con Visual Studio

> *Nota: Esta sección se aplica solo a Windows. Los usuarios de Linux y macOS deben realizar el cambio descrito en el paso 2 a continuación. Guarde el archivo y confirme el cambio en el repositorio local con `git commit`. Por último, inserte el cambio con `git push`, como en la primera sección.*

La aplicación ya se ha implementado desde el shell de comandos. Vamos a usar las herramientas integradas de Visual Studio para implementar una actualización de la aplicación. En segundo plano, Visual Studio consigue lo mismo que las herramientas de línea de comandos, pero dentro de la interfaz de usuario familiar de Visual Studio.

1. Abra *SimpleFeedReader.sln* en Visual Studio.
2. En el Explorador de soluciones, abra *Pages\Index.cshtml*. Cambio de `<h2>Simple Feed Reader</h2>` a `<h2>Simple Feed Reader - V2</h2>`.
3. Presione **Ctrl**+**Mayús**+**B** para compilar la aplicación.
4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, a continuación, en **Publicar**.

    ![Captura de pantalla que muestra el clic con el botón derecho, Publicar](./media/deploying-to-app-service/publish.png)
5. Visual Studio puede crear un nuevo recurso de App Service, pero esta actualización se publicará en la implementación existente. En el cuadro de diálogo **Elegir un destino de publicación**, seleccione **App Service** en la lista de la izquierda y, a continuación, seleccione **Seleccionar existentes**. Haga clic en **Publicar**.
6. En el cuadro de diálogo **App Service**, confirme que la cuenta profesional o de Microsoft utilizada para crear la suscripción de Azure se muestre en la esquina superior derecha. Si no es así, haga clic en el menú desplegable y agréguelo.
7. Conforme que se selecciona la **suscripción** de Azure correcta. Para **Vista**, seleccione **Grupo de recursos**. Expanda el grupo de recursos **AzureTutorial** y, a continuación, seleccione la aplicación web existente. Haga clic en **Aceptar**.

    ![Captura de pantalla que muestra el cuadro de diálogo Publicar App Service](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio compila e implementa la aplicación en Azure. Navegue hasta la dirección URL de la aplicación web. Valide que la modificación del elemento `<h2>` esté activa.

![La aplicación con el título modificado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Ranuras de implementación

Las ranuras de implementación admiten el almacenamiento provisional de los cambios sin afectar a la aplicación que se ejecuta en producción. Una vez que un equipo de control de calidad valida la versión de ensayo de la aplicación, se pueden intercambiar los espacios de producción y de ensayo. La aplicación en almacenamiento provisional se promueve a producción de esta manera. En los pasos siguientes se crea un espacio de ensayo, se implementan algunos cambios en él y se intercambia el espacio de ensayo con producción después de la comprobación.

1. Inicie sesión en [Azure Cloud Shell](https://shell.azure.com/bash), si aún no ha iniciado sesión.
2. Cree el espacio de ensayo.

    a. Cree una ranura de implementación con el nombre *almacenamiento provisional*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configure el espacio de ensayo para usar la implementación de Git local y obtener la dirección URL de implementación de **almacenamiento provisional**. **Tenga en cuenta esta dirección URL como referencia más adelante**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Muestre la dirección URL del espacio de ensayo. Vaya a la dirección URL para ver el espacio de ensayo vacío. **Tenga en cuenta esta dirección URL como referencia más adelante**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. En un editor de texto o Visual Studio, vuelva a modificar *Pages/Index.cshtml* para que el elemento `<h2>` lea `<h2>Simple Feed Reader - V3</h2>` y guarde el archivo.

4. Confirme el archivo en el repositorio de Git local con la página **Cambios** de la pestaña *Team Explorer* de Visual Studio, o bien especifique lo siguiente con el shell de comandos del equipo local:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Con el shell de comandos del equipo local, agregue la dirección URL de implementación de ensayo como un Git remoto y envíe los cambios confirmados:

    a. Agregue la dirección URL remota para el almacenamiento provisional en el repositorio local de Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Inserte la rama *maestra* local en la rama *maestra* de *azure-staging* remoto.

    ```console
    git push azure-staging master
    ```

    Espere mientras Azure compila e implementa la aplicación.

6. Para comprobar que V3 se ha implementado en el espacio de ensayo, abra dos ventanas del explorador. En una ventana, vaya a la dirección URL de la aplicación web original. En la otra ventana, vaya a la dirección URL de la aplicación web de almacenamiento provisional. La dirección URL de producción ofrece servicio a la versión 2 de la aplicación. La dirección URL de almacenamiento provisional ofrece servicio a la versión 3 de la aplicación.

    ![Captura de pantalla que compara las ventanas del explorador](./media/deploying-to-app-service/ready-to-swap.png)

7. En Cloud Shell, intercambie el espacio de ensayo verificado o preparado a producción.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Para comprobar que se ha producido el intercambio, actualice las dos ventanas del explorador.

    ![Comparar las ventanas del explorador después del intercambio](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Resumen

En esta sección, se completaron las tareas siguientes:

* Se descargó y compiló la aplicación de ejemplo.
* Se creó una aplicación web de Azure App Service con Azure Cloud Shell.
* Se implementó la aplicación de ejemplo en Azure con Git.
* Se implementó un cambio en la aplicación mediante Visual Studio.
* Se agregó un espacio de ensayo a la aplicación web.
* Se implementó una actualización en el espacio de ensayo.
* Se intercambiaron los espacios de ensayo y producción.

En la siguiente sección, aprenderá a crear una canalización de DevOps con Azure Pipelines.

## <a name="additional-reading"></a>Lecturas adicionales

* [Introducción a Web Apps](/azure/app-service/app-service-web-overview)
* [Creación de una aplicación .NET Core y SQL Database en Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configuración de credenciales de implementación para Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Configuración de entornos de ensayo en Azure App Service](/azure/app-service/web-sites-staged-publishing)
