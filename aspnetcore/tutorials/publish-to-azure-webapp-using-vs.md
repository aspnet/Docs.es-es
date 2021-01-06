---
title: Publicar una aplicación de ASP.NET Core en Azure con Visual Studio
author: rick-anderson
description: Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- appsettings.json
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060227"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publicar una aplicación de ASP.NET Core en Azure con Visual Studio

Por [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Consulte [Publicación de una aplicación web en Azure App Service mediante Visual Studio para Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) si trabaja en macOS.

Para solucionar un problema de implementación de App Service, vea <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurar

* Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una. 

## <a name="create-a-web-app"></a>Creación de una aplicación web

En la página de inicio de Visual Studio, seleccione **Archivo > Nuevo > Proyecto...** .

![menú Archivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Complete el cuadro de diálogo **Nuevo proyecto**:

* Seleccione **Aplicación web de ASP.NET Core**.
* Seleccione **Siguiente**.

![Cuadro de diálogo Nuevo proyecto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

En el cuadro de diálogo **Nueva aplicación web de ASP.NET Core**, haga lo siguiente:

* Seleccione **Aplicación web**.
* En Autenticación, seleccione **Cambiar**.

![Cuadro de diálogo Nueva aplicación web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Se mostrará el cuadro de diálogo **Cambiar autenticación**. 

* Seleccione **Cuentas de usuario individuales**.
* Seleccione **Aceptar** para volver a **Nueva aplicación web ASP.NET Core** y, luego, elija **Crear**.

![Cuadro de diálogo de autenticación web de ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crea la solución.

## <a name="run-the-app"></a>Ejecutar la aplicación

* Presione CTRL+F5 para ejecutar el proyecto.
* Pruebe el vínculo **Privacidad**.

![Aplicación web abierta en Microsoft Edge en host local](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrar un usuario

* Seleccione **Registrar** y registre a un usuario nuevo. Puede usar una dirección de correo electrónico ficticia. Al enviar la información, la página mostrará el error siguiente:

    *"Error en una operación de base de datos al procesar la solicitud. La aplicación de las migraciones existentes para el contexto de base de datos de la aplicación puede solucionar este problema".*
* Seleccione **Aplicar migraciones** y, una vez actualizada la página, vuelva a cargarla.

![Error en una operación de base de datos al procesar la solicitud. La aplicación de las migraciones existentes para el contexto de base de datos de la aplicación puede solucionar este problema.](publish-to-azure-webapp-using-vs/_static/mig.png)

La aplicación muestra el correo electrónico usado para registrar el nuevo usuario y un vínculo **Cerrar sesión**.

![Aplicación web abierta en Microsoft Edge. El vínculo Registrar se sustituye por el texto Hello user1@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Implementación de la aplicación en Azure

Desde el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Publicar...**

![Menú contextual abierto con el vínculo Publicar resaltado](publish-to-azure-webapp-using-vs/_static/pub.png)

En el cuadro de diálogo **Publicar**:

* Seleccione **Azure**.
* Seleccione **Siguiente**.

![Cuadro de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

En el cuadro de diálogo **Publicar**:

* Seleccione **Azure App Service (Linux)** .
* Seleccione **Siguiente**.

![Cuadro de diálogo Publicar: selección de un servicio de Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

En el cuadro de diálogo **Publicar**, seleccione **Create a new Azure App Service** (Crear una instancia de Azure App Service):

![Cuadro de diálogo Publicar: selección de una instancia de servicio de Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

Aparece el cuadro de diálogo **Crear servicio de aplicaciones**:

* Se rellenan los campos de entrada **Nombre de la aplicación**, **Grupo de recursos** y **Plan de App Service**. Puede mantener estos nombres o cambiarlos.
* Seleccione **Crear**.

![Cuadro de diálogo Crear servicio de aplicaciones](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Una vez terminada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro de diálogo **Publicar** vuelve a recibir el foco:

* La nueva instancia que se acaba de crear se selecciona automáticamente.
* Seleccione **Finalizar**.

![Cuadro de diálogo Publicar: selección de la instancia de App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

A continuación, verá la página de **resumen del perfil de publicación**. Visual Studio ha detectado que esta aplicación requiere una base de datos de SQL Server y le pide que la configure. Seleccione **Configurar**.

![Página de resumen del perfil de publicación: configuración de la dependencia de SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

Aparece el cuadro de diálogo **Configurar dependencia**:

* Seleccione **Azure SQL Database**.
* Seleccione **Siguiente**.

![Cuadro de diálogo de configuración de dependencia de SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

En el cuadro de diálogo **Configure Azure SQL database** (Configurar base de datos de Azure SQL), seleccione **Crear una base de datos SQL**.

![Cuadro de diálogo de configuración de una base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql2.png)

Aparece el cuadro de diálogo **Crear una base de datos de Azure SQL**:

* Se rellenan los campos de entrada de datos **Nombre de la base de datos**, **Grupo de recursos**, **Servidor de base de datos** y **Plan de App Service**. Puede mantener estos nombres o cambiarlos.
* Especifique los valores de **Database administrator username** (Nombre de usuario del administrador de la base de datos) y **Database administrator password** (Contraseña del administrador de la base de datos) del **servidor de bases de datos** seleccionado (tenga en cuenta que la cuenta que use debe tener los permisos necesarios para crear la base de datos de Azure SQL).
* Seleccione **Crear**.

![Cuadro de diálogo de nueva base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Una vez finalizada la creación, el cuadro de diálogo se cierra automáticamente y el cuadro de diálogo **Configure Azure SQL Database** (Configurar base de datos de Azure SQL), vuelve a recibir el foco:

* La nueva instancia que se acaba de crear se selecciona automáticamente.
* Seleccione **Siguiente**.

![Cuadro de diálogo de configuración de una base de datos de Azure SQL](publish-to-azure-webapp-using-vs/_static/sql_select.png)

En el paso siguiente del cuadro de diálogo **Configure Azure SQL Database** (Configurar base de datos de Azure SQL):

* Rellene los campos **Database connection user name** (Nombre de usuario de conexión de la base de datos) y **Database connection password** (Contraseña de conexión de la base de datos). Estos son los detalles que utilizará la aplicación para conectarse a la base de datos en tiempo de ejecución. El procedimiento recomendado es evitar el uso de los mismos detalles que el nombre de usuario y la contraseña de administrador usados en el paso anterior.
* Seleccione **Finalizar**.

![Cuadro de diálogo de configuración de una base de datos de Azure SQL, detalles de la cadena de conexión](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

En la página de **resumen del perfil de publicación**, seleccione **Configuración**:

![Página de resumen del perfil de publicación: edición de la configuración](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

En la página **Configuración** del cuadro de diálogo **Publicar**, haga lo siguiente:

* Expanda **Bases de datos** y active **Usar esta cadena de conexión en tiempo de ejecución**.
* Expanda **Migraciones de Entity Framework** y active **Aplicar esta migración al publicar**.

* Seleccione **Guardar**. Visual Studio volverá al cuadro de diálogo **Publicar**. 

![Cuadro de diálogo Publicar: Panel Configuración](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Haga clic en **Publicar**. Visual Studio publica la aplicación en Azure. Cuando la implementación termine, la aplicación se abre en un explorador.

![Cuadro de diálogo Publicar: Panel Configuración](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Actualización de la aplicación

* Edite la página de Razor *Pages/Index.cshtml* y cambie su contenido. Por ejemplo, puede editar el párrafo para que incluya el mensaje "¡Hola, ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Seleccione **Publicar** de nuevo en la página de **resumen del perfil de publicación**.

![Página de resumen del perfil de publicación](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Una vez publicada la aplicación, compruebe que los cambios realizados estén disponibles en Azure.

![Comprobar si se ha completado la tarea](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Limpieza

Cuando haya terminado de probar la aplicación, vaya a [Azure Portal](https://portal.azure.com/) y elimínela.

* Seleccione **Grupos de recursos** y, luego, el grupo de recursos que haya creado.

![Azure Portal: Grupos de recursos en el menú lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* En la página **Grupos de recursos**, seleccione **Eliminar**.

![Azure Portal: página Grupos de recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Escriba el nombre del grupo de recursos y seleccione **Eliminar**. La aplicación y todos los demás recursos que ha creado en este tutorial se han eliminado de Azure.

### <a name="next-steps"></a>Pasos siguientes

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionales

* Para Visual Studio Code, vea [Publicar los perfiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Grupos de recursos de Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>