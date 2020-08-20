---
title: Implementación continua en Azure con Visual Studio y Git con ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla en Azure App Service con Git para una implementación continua.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1f3fca62b39125d9f23042c5929de829ab243865
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633180"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Implementación continua en Azure con Visual Studio y Git con ASP.NET Core

Por [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

En este tutorial se muestra cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla desde Visual Studio en Azure App Service mediante una implementación continua.

Vea también [Creación de la primera canalización con Azure Pipelines](/azure/devops/pipelines/get-started-yaml), donde se explica cómo configurar un flujo de trabajo de entrega continua (CD) para [Azure App Service](/azure/app-service/app-service-web-overview) mediante Azure DevOps Services. Azure Pipelines, un servicio de Azure DevOps Services, simplifica la configuración de una canalización de implementación sólida para publicar actualizaciones de aplicaciones hospedadas en Azure App Service. La canalización se puede configurar desde Azure Portal para crear y ejecutar pruebas, implementarlas en un espacio de ensayo y luego implementarlas en un entorno de producción.

> [!NOTE]
> Para realizar este tutorial, necesita una cuenta de Microsoft Azure. Para obtener una, [active las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) o [regístrese para una prueba gratuita](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se da por hecho que está instalado el siguiente software:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) para Windows

## <a name="create-an-aspnet-core-web-app"></a>Crear una aplicación web de ASP.NET Core

1. Inicie Visual Studio.

1. En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.

1. Seleccione la plantilla de proyecto **Aplicación web ASP.NET Core**. Aparece en **Instalados** > **Plantillas** > **Visual C#**  >  **.NET Core**. Dé un nombre al proyecto `SampleWebAppDemo`. Seleccione la opción **Crear nuevo repositorio de Git** y haga clic en **Aceptar**.

   ![Cuadro de diálogo Nuevo proyecto](azure-continuous-deployment/_static/01-new-project.png)

1. En el cuadro de diálogo **Nuevo proyecto ASP.NET Core**, seleccione la plantilla **Vacía** de ASP.NET Core y haga clic en **Aceptar**.

   ![Cuadro de diálogo Nuevo proyecto ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> La versión más reciente de .NET Core es la 2.0.

### <a name="running-the-web-app-locally"></a>Ejecutar la aplicación web de forma local

1. Cuando Visual Studio haya acabado de crear la aplicación, ejecútela seleccionando **Depurar** > **Iniciar depuración**. Como alternativa, presione **F5**.

   Visual Studio y la aplicación nueva pueden tardar un poco en inicializarse. Una vez completada la operación, el explorador muestra la aplicación en ejecución.

   ![Ventana del explorador en la que aparece la aplicación en ejecución que muestra "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Después de revisar la aplicación web en ejecución, cierre el explorador y seleccione el icono "Detener depuración" de la barra de herramientas de Visual Studio para detener la aplicación.

## <a name="create-a-web-app-in-the-azure-portal"></a>Crear una aplicación web en Azure Portal

Los pasos siguientes le permiten crear una aplicación web en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Nuevo** en la parte superior izquierda de la interfaz del portal.

1. Seleccione **Web y móvil** > **Aplicación web**.

   ![Microsoft Azure Portal: botón Nuevo: Web y móvil en Marketplace: Botón Aplicación web en Aplicaciones destacadas](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. En la hoja **Aplicación web**, escriba un valor único para el **nombre del servicio de aplicaciones**.

   ![Hoja Aplicación web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > El **nombre de App Service** debe ser único. El portal aplica esta regla cuando se proporciona el nombre. Si proporciona un valor diferente, sustituya ese valor por cada aparición de **SampleWebAppDemo** en este tutorial.

   También en la hoja **Aplicación web**, seleccione un **plan o ubicación existente de App Service** o bien cree uno. Si va a crear un plan, seleccione el plan de tarifa, la ubicación y otras opciones. Para más información sobre los planes de App Service, consulte [Introducción detallada a los planes de Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Seleccione **Crear**. Azure aprovisionará e iniciará la aplicación web.

   ![Azure Portal: Hoja SampleWebAppDemo01 de Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Habilitar la publicación de Git para la nueva aplicación web

Git es un sistema distribuido de control de versiones que se puede usar para implementar una aplicación web de Azure App Service. El código de aplicación web se almacena en un repositorio de Git local y se implementa en Azure mediante la inserción en un repositorio remoto.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **App Services** para ver una lista los servicios de aplicaciones asociados a su suscripción de Azure.

1. Seleccione la aplicación web que creó en la sección anterior de este tutorial.

1. En la hoja **Implementación**, seleccione **Opciones de implementación** > **Elegir origen** > **Repositorio de Git local**.

   ![Hoja Configuración: Hoja Origen de implementación: Hoja Elegir origen](azure-continuous-deployment/_static/deployment-options.png)

1. Seleccione **Aceptar**.

1. Si no ha configurado antes las credenciales de implementación para publicar una aplicación web u otra aplicación de App Service, configúrelas ahora:

   * Seleccione **Configuración** > **Credenciales de implementación**. Se muestra la hoja **Configurar credenciales de implementación**.
   * Cree un nombre de usuario y una contraseña. Guarde la contraseña; la necesitará más adelante al configurar Git.
   * Seleccione **Guardar**.

1. En la hoja **Aplicación web**, seleccione **Configuración** > **Propiedades**. La dirección URL del repositorio de Git remoto en el que va a efectuar la implementación aparece en **Dirección URL de Git**.

1. Copie el valor de **Dirección URL de Git** para usarlo más adelante en el tutorial.

   ![Azure Portal: Hoja Propiedades de la aplicación](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicación de la aplicación web en Azure App Service

En esta sección, creará un repositorio de Git local con Visual Studio y lo insertará desde ese repositorio en Azure para implementar la aplicación web. Los pasos son los siguientes:

* Agrega la configuración de repositorio remoto mediante el valor de dirección URL de GIT, de modo que el repositorio local se pueda implementar en Azure
* Confirmar los cambios en el proyecto
* Insertar los cambios en el proyecto desde el repositorio local hasta el repositorio remoto en Azure

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Solución 'SampleWebAppDemo'** y seleccione **Confirmar**. Se muestra **Team Explorer**.

   ![Pestaña Team Explorer: Conexión](azure-continuous-deployment/_static/10-team-explorer.png)

1. En **Team Explorer**, seleccione **Inicio** (icono Inicio) > **Configuración** > **Configuración del repositorio**.

1. En la sección **Remotos** de **Configuración del repositorio**, seleccione **Agregar**. Aparece el cuadro de diálogo **Agregar remoto**.

1. Establezca el **nombre** del repositorio remoto en **Azure-SampleApp**.

1. Establezca el valor de **Recuperar** en la **dirección URL de Git** que copió de Azure anteriormente en este tutorial. Tenga en cuenta que esta es la dirección URL que termina en **.git**.

   ![Cuadro de diálogo Editar Azure-SampleApp remoto](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Como alternativa, especifique el repositorio remoto desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba el comando. Ejemplo:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Seleccione **Inicio** (icono de Inicio) > **Configuración** > **Configuración global**. Confirme que se establecen el nombre y la direcciones de correo electrónico. Seleccione **Actualizar** si es necesario.

1. Seleccione **Inicio** > **Cambios** para volver a la vista **Cambios**.

1. Escriba un mensaje de confirmación, como **Inserción inicial 1** y seleccione **Confirmar**. Esta acción crea una *confirmación* localmente.

   ![Pestaña Team Explorer: Conexión](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Como alternativa, puede confirmar los cambios desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba los comandos de Git. Ejemplo:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Seleccione **Inicio** > **Sincronizar** > **Acciones** > **Abrir símbolo del sistema**. El símbolo del sistema se abre en el directorio del proyecto.

1. Escriba el siguiente comando en la ventana de comandos:

   `git push -u Azure-SampleApp master`

1. Escriba la contraseña de sus **credenciales de implementación** de Azure que creó anteriormente en Azure.

   Este comando inicia el proceso de inserción de los archivos locales del proyecto en Azure. La salida del comando anterior finaliza con un mensaje que indica que la implementación se efectuó correctamente.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Si se requiere la colaboración en el proyecto, considere la posibilidad de insertarlos en [GitHub](https://github.com) antes de insertarlos en Azure.
 
### <a name="verify-the-active-deployment"></a>Comprobar la implementación activa

Compruebe que la transferencia de la aplicación web desde el entorno local a Azure es correcta.

En [Azure Portal](https://portal.azure.com), seleccione la aplicación web. Después, seleccione **Implementación** > **Opciones de implementación**.

![Azure Portal: Hoja Configuración: Hoja Implementaciones donde se muestra una implementación correcta](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Ejecutar la aplicación en Azure

Ahora que la aplicación web se ha implementado en Azure, ejecute la aplicación.

Esto puede realizarse de dos maneras:

* En Azure Portal, busque la hoja de la aplicación web. Seleccione **aminar** para ver la aplicación en el explorador predeterminado.
* Abra un explorador y escriba la dirección URL de la aplicación web. Ejemplo: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Actualizar la aplicación web y volver a publicarla

Después de realizar cambios en el código local, vuelva a publicar la aplicación:

1. En el **Explorador de soluciones** de Visual Studio, abra el archivo *Startup.cs*.

1. En el método `Configure`, modifique el método `Response.WriteAsync` para que aparezca del siguiente modo:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Guarde los cambios en *Startup.cs*.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Solución 'SampleWebAppDemo'** y seleccione **Confirmar**. Se muestra **Team Explorer**.

1. Escriba un mensaje de confirmación, como `Update #2`.

1. Presione el botón **Confirmar** para confirmar los cambios del proyecto.

1. Seleccione **Inicio** > **Sincronizar** > **Acciones** > **Inserción**.

> [!NOTE]
> Como alternativa, inserte los cambios desde la **ventana de comandos**. Para ello, abra la **ventana de comandos**, cambie al directorio del proyecto y escriba un comando de Git. Ejemplo:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Ver la aplicación web actualizada en Azure

Para ver la aplicación web actualizada, seleccione **Examinar** en la hoja de la aplicación web en Azure Portal o abra un explorador y escriba la dirección URL de la aplicación web. Ejemplo: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Recursos adicionales

* [Creación de la primera canalización con Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Proyecto Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
