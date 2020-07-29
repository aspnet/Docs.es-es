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
# <a name="continuous-integration-and-deployment"></a>Integración e implementación continuas

En el capítulo anterior se ha creado un repositorio de Git local para la aplicación Simple Feed Reader. En este capítulo se va a publicar ese código en un repositorio de GitHub y a crear una canalización de Azure DevOps Services mediante Azure Pipelines. La canalización habilita las compilaciones y las implementaciones continuas de la aplicación. Cualquier confirmación en el repositorio de GitHub desencadena una compilación y una implementación en el espacio de ensayo de la aplicación web de Azure.

En esta sección se van a realizar las tareas siguientes:

* Publicar el código de la aplicación en GitHub
* Desconectar la implementación de Git local
* Crear una organización de Azure DevOps
* Crear un proyecto de equipo en Azure DevOps Services
* Crear una definición de compilación
* Creación de una canalización de versión
* Confirmar cambios en GitHub e implementarlos automáticamente en Azure
* Examinar la canalización de Azure Pipelines

## <a name="publish-the-apps-code-to-github"></a>Publicar el código de la aplicación en GitHub

1. Abra una ventana del explorador y vaya a `https://github.com`.
1. Haga clic en la lista desplegable **+** del encabezado y seleccione **Nuevo repositorio**:

    ![Opción Nuevo repositorio de GitHub](media/cicd/github-new-repo.png)

1. Seleccione la cuenta en la lista desplegable **Propietario** y escriba *simple-feed-reader* en el cuadro de texto **Nombre del repositorio**.
1. Haga clic en el botón **Crear repositorio**.
1. Abra el shell de comandos del equipo local. Vaya al directorio en el que está almacenado el repositorio de Git *simple-feed-reader*.
1. Cambie el nombre del elemento *origin* existente remoto a *upstream*. Ejecute el siguiente comando:

    ```console
    git remote rename origin upstream
    ```

1. Agregue un nuevo elemento *origin* remoto que apunte a la copia del repositorio en GitHub. Ejecute el siguiente comando:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publique el repositorio de Git local en el repositorio de GitHub recién creado. Ejecute el siguiente comando:

    ```console
    git push -u origin master
    ```

1. Abra una ventana del explorador y vaya a `https://github.com/<GitHub_username>/simple-feed-reader/`. Valide que el código aparece en el repositorio de GitHub.

## <a name="disconnect-local-git-deployment"></a>Desconectar la implementación de Git local

Quite la implementación de Git local con los pasos siguientes. Azure Pipelines (un servicio de Azure DevOps) reemplaza y aumenta esa funcionalidad.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya a la aplicación web de *almacenamiento provisional (mywebapp\<unique_number\>/staging)* . La aplicación web se puede encontrar rápidamente si se escribe *staging* en el cuadro de búsqueda del portal:

    ![Término de búsqueda de la aplicación web staging](media/cicd/portal-search-box.png)

1. Haga clic en **Centro de implementación**. Aparece un nuevo panel. Haga clic en **Desconectar** para quitar la configuración de control de código fuente de Git local agregada en el capítulo anterior. Para confirmar la operación de eliminación, haga clic en el botón **Sí**.
1. Vaya a la instancia de App Service *mywebapp<número_único>* . Como recordatorio, el cuadro de búsqueda del portal se puede usar para buscar rápidamente la instancia de App Service.
1. Haga clic en **Centro de implementación**. Aparece un nuevo panel. Haga clic en **Desconectar** para quitar la configuración de control de código fuente de Git local agregada en el capítulo anterior. Para confirmar la operación de eliminación, haga clic en el botón **Sí**.

## <a name="create-an-azure-devops-organization"></a>Crear una organización de Azure DevOps

1. Abra un explorador y vaya a la [página de creación de organización de Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Escriba un nombre único en el cuadro de texto **Elija un nombre fácil de recordar** para formar la dirección URL para acceder a la organización de Azure DevOps.
1. Seleccione el botón de radio **Git**, ya que el código está hospedado en un repositorio de GitHub.
1. Haga clic en el botón **Continuar**. Tras una breve espera, se crean una cuenta y un proyecto de equipo que se denomina *MyFirstProject*.

    ![Página de creación de organización de Azure DevOps](media/cicd/vsts-account-creation.png)

1. Abra el correo electrónico de confirmación que indica que la organización y el proyecto de Azure DevOps están listos para su uso. Haga clic en el botón **Inicie su proyecto**:

    ![Botón Inicie su proyecto](media/cicd/vsts-start-project.png)

1. Se abre un explorador para *\<account_name\>.visualstudio.com*. Haga clic en el vínculo *MyFirstProject* para empezar a configurar la canalización de DevOps del proyecto.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configurar la canalización de Azure Pipelines

Hay tres pasos diferentes que se deben realizar. La realización de los pasos de las tres secciones siguientes da lugar a una canalización de DevOps operativa.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Conceder acceso de Azure DevOps al repositorio de GitHub

1. Expanda el acordeón **o compilar código desde un repositorio externo**. Haga clic en el botón **Configurar compilación**:

    ![Botón de configuración de compilación](media/cicd/vsts-setup-build.png)

1. Seleccione la opción **GitHub** en la sección **Seleccionar un origen**:

    ![Seleccionar un origen - GitHub](media/cicd/vsts-select-source.png)

1. Se necesita autorización para que Azure DevOps pueda acceder al repositorio de GitHub. Escriba *<nombreUsuario_GitHub> GitHub connection* en el cuadro de texto **Nombre de conexión**. Por ejemplo:

    ![Nombre de conexión de GitHub](media/cicd/vsts-repo-authz.png)

1. Si la autenticación en dos fases está habilitada en la cuenta de GitHub, se requiere un token de acceso personal. En ese caso, haga clic en el vínculo **Autorizar con un token de acceso personal de GitHub**. Vea las [instrucciones oficiales de creación de tokens de acceso personales de GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obtener ayuda. Solo se necesita el ámbito *repo* de permisos. De lo contrario, haga clic en el botón **Autorizar el uso de OAuth**.
1. Cuando se le pida, inicie sesión en la cuenta de GitHub. Luego, seleccione Autorizar para conceder acceso a la organización de Azure DevOps. Si la operación se realiza correctamente, se crea un nuevo punto de conexión de servicio.
1. Haga clic en el botón de puntos suspensivos situado junto al botón **Repositorio**. En la lista, seleccione el repositorio *<nombreUsuario_GitHub>/simple-feed-reader*. Haga clic en el botón **Seleccionar**.
1. Seleccione la rama *master* de la lista desplegable **Rama predeterminada para compilaciones manuales y programadas**. Haga clic en el botón **Continuar**. Aparece la página de selección de plantilla.

### <a name="create-the-build-definition"></a>Creación de la definición de compilación

1. En la página de selección de plantilla, escriba *ASP.NET Core* en el cuadro de búsqueda:

    ![Búsqueda de ASP.NET Core en la página de plantilla](media/cicd/vsts-template-selection.png)

1. Aparecen los resultados de búsqueda de la plantilla. Mantenga el mouse sobre la plantilla **ASP.NET Core** y haga clic en el botón **Aplicar**.
1. Aparece la pestaña **Tareas** de la definición de compilación. Haga clic en la pestaña **Desencadenadores**.
1. Active la casilla **Habilitar la integración continua**. En la sección **Filtros de rama**, confirme que la lista desplegable **Tipo** está establecida en *Incluir*. Establezca la lista desplegable **Especificación de rama** en *master*.

    ![Habilitación de la configuración de integración continua](media/cicd/vsts-enable-ci.png)

    Esta configuración hace que se desencadene una compilación cada vez que se inserta cualquier cambio en la rama *master* del repositorio de GitHub. La integración continua se prueba en la sección [Confirmar cambios en GitHub e implementar automáticamente en Azure](#commit-changes-to-github-and-automatically-deploy-to-azure).

1. Haga clic en el botón **Guardar y poner en cola** y seleccione la opción **Guardar**:

    ![Botón Guardar](media/cicd/vsts-save-build.png)

1. Aparece el siguiente cuadro de diálogo modal:

    ![Guardado de la definición de compilación - cuadro de diálogo modal](media/cicd/vsts-save-modal.png)

    Use la carpeta predeterminada de *\\* y haga clic en el botón **Guardar**.

### <a name="create-the-release-pipeline"></a>Creación de la canalización de versión

1. Haga clic en la pestaña **Versiones** del proyecto de equipo. Haga clic en el botón **Nueva canalización**.

    ![Pestaña Versiones - botón de nueva definición](media/cicd/vsts-new-release-definition.png)

    Aparece el panel de selección de plantilla.

1. En la página de selección de plantilla, escriba *App Service* en el cuadro de búsqueda:

    ![Cuadro de búsqueda de plantilla de canalización de versión](media/cicd/vsts-release-template-search.png)

1. Aparecen los resultados de búsqueda de la plantilla. Mantenga el mouse sobre la plantilla **Implementación de Azure App Service con espacio** y haga clic en el botón **Aplicar**. Aparece la pestaña **Canalización** de la canalización de versión.

    ![Pestaña Canalización de la canalización de versión](media/cicd/vsts-release-definition-pipeline.png)

1. Haga clic en el botón **Agregar** del cuadro **Artefactos**. Aparece el panel **Agregar artefacto**:

    ![Canalización de versión - panel Agregar artefacto](media/cicd/vsts-release-add-artifact.png)

1. Seleccione el icono **Compilación** de la sección **Tipo de origen**. Este tipo permite la vinculación de la canalización de versión a la definición de compilación.
1. Seleccione *MyFirstProject* en la lista desplegable **Proyecto**.
1. Seleccione el nombre de la definición de compilación, *MyFirstProject-ASP.NET Core-CI*, en la lista desplegable **Origen (definición de compilación)** .
1. En la lista desplegable *Versión predeterminada*, seleccione **Más reciente**. Esta opción compila los artefactos generados por la última ejecución de la definición de compilación.
1. Reemplace el texto del cuadro de texto **Alias de origen** por *Drop*.
1. Haga clic en el botón **Agregar**. La sección **Artefactos** se actualiza para mostrar los cambios.
1. Haga clic en el icono de rayo para habilitar las implementaciones continuas:

    ![Canalización de versión Artefactos - icono de rayo](media/cicd/vsts-artifacts-lightning-bolt.png)

    Con esta opción habilitada, se produce una implementación cada vez que hay una nueva compilación disponible.
1. Aparece un panel **Desencadenador de implementación continua** a la derecha. Haga clic en el botón de alternancia para habilitar la característica. No es necesario habilitar **Desencadenador de solicitud de incorporación de cambios**.
1. Haga clic en la lista desplegable **Agregar** de la sección **Filtros de rama de compilación**. Seleccione la opción **Rama predeterminada de la definición de compilación**. Este filtro hace que la versión se desencadene solo para una compilación de la rama *master* del repositorio de GitHub.
1. Haga clic en el botón **Guardar**. Haga clic en el botón **Aceptar** del cuadro de diálogo modal **Guardar** resultante.
1. Haga clic en el cuadro **Environment 1**. Aparece un panel **Entorno** a la derecha. Cambie el texto de *Environment 1* en el cuadro de texto **Nombre del entorno** a *Production*.

   ![Canalización de versión - cuadro de texto Nombre del entorno](media/cicd/vsts-environment-name-textbox.png)

1. Haga clic en el vínculo **1 fase, 2 tareas** del cuadro **Production**:

    ![Canalización de versión - Production environment link.png](media/cicd/vsts-production-link.png)

    Aparece la pestaña **Tareas** del entorno.
1. Haga clic en la tarea **Deploy Azure App Service to Slot** (Implementar Azure App Service en espacio). Su configuración aparece en un panel a la derecha.
1. Seleccione la suscripción de Azure asociada a la instancia de App Service en la lista desplegable **Suscripción de Azure**. Una vez seleccionada, haga clic en el botón **Autorizar**.
1. Seleccione *Aplicación web* en la lista desplegable **Tipo de aplicación**.
1. Seleccione *mywebapp/<número_único/>* en la lista desplegable **Nombre de App Service**.
1. Seleccione *AzureTutorial* en la lista desplegable **Grupo de recursos**.
1. Seleccione *staging* en la lista desplegable **Espacio**.
1. Haga clic en el botón **Guardar**.
1. Mantenga el mouse sobre el nombre de la canalización de versión predeterminada. Haga clic en el icono de lápiz para editarlo. Use *MyFirstProject-ASP.NET Core-CD* como nombre.

    ![Nombre de la canalización de versión](media/cicd/vsts-release-definition-name.png)

1. Haga clic en el botón **Guardar**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Confirmar cambios en GitHub e implementarlos automáticamente en Azure

1. Abra *SimpleFeedReader.sln* en Visual Studio.
1. En el Explorador de soluciones, abra *Pages\Index.cshtml*. Cambio de `<h2>Simple Feed Reader - V3</h2>` a `<h2>Simple Feed Reader - V4</h2>`.
1. Presione **Ctrl**+**Mayús**+**B** para compilar la aplicación.
1. Confirme el archivo en el repositorio de GitHub. Use la página **Cambios** de la pestaña *Team Explorer* de Visual Studio, o bien ejecute lo siguiente con el shell de comandos del equipo local:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Inserte el cambio en la rama *master* del elemento *origin* remoto del repositorio de GitHub:

    ```console
    git push origin master
    ```

    La confirmación aparece en la rama *master* del repositorio de GitHub:

    ![Confirmación de GitHub en la rama master](media/cicd/github-commit.png)

    La compilación se desencadena, ya que la integración continua está habilitada en la pestaña **Desencadenadores** de la definición de compilación:

    ![Habilitación de la integración continua](media/cicd/enable-ci.png)

1. Vaya a la pestaña **En cola** de la página **Azure Pipelines** > **Compilaciones** de Azure DevOps Services. La compilación en cola muestra la rama y la confirmación que ha desencadenado la compilación:

    ![Compilación en cola](media/cicd/build-queued.png)

1. Una vez que la compilación se realiza correctamente, se produce una implementación en Azure. Vaya a la aplicación en el explorador. Observe que el texto "V4" aparece en el encabezado:

    ![Aplicación actualizada](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Examinar la canalización de Azure Pipelines

### <a name="build-definition"></a>Definición de compilación

Se ha creado una definición de compilación con el nombre *MyFirstProject-ASP.NET Core-CI*. Al finalizar, la compilación genera un archivo *.zip* que incluye los recursos que se van a publicar. La canalización de versión implementa esos recursos en Azure.

La pestaña **Tareas** de la definición de compilación muestra los pasos individuales que se están usando. Hay cinco tareas de compilación.

![Tareas de definición de compilación](media/cicd/build-definition-tasks.png)

1. **Restaurar** &mdash; Ejecuta el comando `dotnet restore` para restaurar los paquetes NuGet de la aplicación. La fuente de paquetes predeterminada empleada es nuget.org.
1. **Compilar** &mdash; Ejecuta el comando `dotnet build --configuration release` para compilar el código de la aplicación. Esta opción `--configuration` se usa para generar una versión optimizada del código adecuada para la implementación en un entorno de producción. Modifique la variable *BuildConfiguration* en la pestaña **Variables** de la definición de compilación si, por ejemplo, se necesita una configuración de depuración.
1. **Probar** &mdash; Ejecuta el comando `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` para ejecutar las pruebas unitarias de la aplicación. Las pruebas unitarias se ejecutan en cualquier proyecto de C# que coincida con el patrón global `**/*Tests/*.csproj`. Los resultados de las pruebas se guardan en un archivo *.trx* en la ubicación especificada por la opción `--results-directory`. Si se produce un error en alguna prueba, ocurre lo mismo en la compilación, que no se implementa.

    > [!NOTE]
    > Para comprobar que las pruebas unitarias funcionan, modifique *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* para interrumpir una de las pruebas a propósito. Por ejemplo, cambie `Assert.True(result.Count > 0);` por `Assert.False(result.Count > 0);` en el método `Returns_News_Stories_Given_Valid_Uri`. Confirme e inserte el cambio en GitHub. La compilación se desencadena y se produce un error. El estado de la canalización de compilación cambia a **error**. Revierta el cambio, confirme e inserte de nuevo. La compilación se realiza correctamente.

1. **Publicar** &mdash; Ejecuta el comando `dotnet publish --configuration release --output <local_path_on_build_agent>` para generar un archivo *.zip* con los artefactos que se van a implementar. La opción `--output` especifica la ubicación de publicación del archivo *.zip*. Esa ubicación se especifica al pasar una [variable predefinida](/azure/devops/pipelines/build/variables) denominada `$(build.artifactstagingdirectory)`. Esa variable se expande a una ruta de acceso local, como *c:\agent\_work\1\a*, en el agente de compilación.
1. **Publicar artefacto** &mdash; Publica el archivo *.zip* generado por la tarea **Publicar**. La tarea acepta la ubicación del archivo *.zip* como parámetro, que es la variable predefinida `$(build.artifactstagingdirectory)`. El archivo *.zip* se publica como una carpeta denominada *drop*.

Haga clic en el vínculo **Resumen** de la definición de compilación para ver un historial de compilaciones con la definición:

![Captura de pantalla que muestra el historial de definiciones de compilación](media/cicd/build-definition-summary.png)

En la página resultante, haga clic en el vínculo correspondiente al número de compilación único:

![Captura de pantalla que muestra la página de resumen de definiciones de compilación](media/cicd/build-definition-completed.png)

Se muestra un resumen de esta compilación concreta. Haga clic en la pestaña **Artefactos** y observe que se muestra la carpeta *drop* generada por la compilación:

![Captura de pantalla que muestra los artefactos de la definición de compilación - carpeta drop](media/cicd/build-definition-artifacts.png)

Use los vínculos **Descargar** y **Explorar** para inspeccionar los artefactos publicados.

### <a name="release-pipeline"></a>Canalización de versión

Se ha creado una canalización de versión con el nombre *MyFirstProject-ASP.NET Core-CI*:

![Captura de pantalla que muestra información general de la canalización de versión](media/cicd/release-definition-overview.png)

Los dos componentes principales de la canalización de versión son los **Artefactos** y los **Entornos**. Al hacer clic en el cuadro de la sección **Artefactos**, se revela el siguiente panel:

![Captura de pantalla que muestra los artefactos de la canalización de versión](media/cicd/release-definition-artifacts.png)

El valor **Origen (definición de compilación)** representa la definición de compilación a la que está vinculada esta canalización de versión. El archivo *.zip* generado por una ejecución correcta de la definición de compilación se proporciona al entorno *Production* para su implementación en Azure. Haga clic en el vínculo *1 fase, 2 tareas* del cuadro del entorno *Production* para ver las tareas de canalización de versión:

![Captura de pantalla que muestra las tareas de la canalización de versión](media/cicd/release-definition-tasks.png)

La canalización de versión consta de dos tareas: *Deploy Azure App Service to Slot* (Implementar Azure App Service en espacio) y *Manage Azure App Service - Slot Swap* (Administrar Azure App Service: intercambio de espacio). Al hacer clic en la primera tarea, se revela la siguiente configuración de tarea:

![Captura de pantalla que muestra la tarea de implementación de la canalización de versión](media/cicd/release-definition-task1.png)

La suscripción de Azure, el tipo de servicio, el nombre de la aplicación web, el grupo de recursos y el espacio de implementación se definen en la tarea de implementación. El cuadro de texto **Paquete o carpeta** contiene la ruta de acceso del archivo *.zip* que se va a extraer e implementar en el *espacio de ensayo* de la aplicación web *mywebapp\<unique_number\>* .

Al hacer clic en la tarea de intercambio de espacio, se revela la siguiente configuración de tarea:

![Captura de pantalla que muestra la tarea de intercambio de espacio de la canalización de versión](media/cicd/release-definition-task2.png)

Se proporcionan los detalles de la suscripción, el grupo de recursos, el tipo de servicio, el nombre de la aplicación web y el espacio de implementación. La casilla **Intercambiar con producción** está activada. Por lo tanto, los bits implementados en el *espacio de ensayo* se intercambian en el entorno de producción.

## <a name="additional-reading"></a>Lecturas adicionales

* [Creación de la primera canalización con Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Compilación y proyecto de .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Implementación de una aplicación web con Azure Pipelines](/azure/devops/pipelines/targets/webapp)
