---
title: 'Supervisión y depuración: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Supervisión y depuración del código como parte de una solución de DevOps con ASP.NET Core y Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/monitor
ms.openlocfilehash: 98d7c820b20f4f4c6b57f283ef718ce056dcdcae
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130501"
---
# <a name="monitor-and-debug"></a>Supervisión y depuración

Una vez implementada la aplicación y después de compilar una canalización de DevOps, es importante comprender cómo supervisar y solucionar problemas de la aplicación.

En esta sección, completará las tareas siguientes:

* Buscar datos básicos de supervisión y solución de problemas en Azure Portal
* Obtener información sobre cómo Azure Monitor proporciona una visión más profunda de las métricas en todos los servicios de Azure
* Conectar la aplicación web con Application Insights para la generación de perfiles de aplicaciones
* Activar el registro y saber dónde descargar registros
* Streaming de registros en tiempo real
* Obtener información sobre dónde configurar alertas
* Obtener información sobre la depuración remota de aplicaciones web de Azure App Service

## <a name="basic-monitoring-and-troubleshooting"></a>Supervisión básica y solución de problemas

Las aplicaciones web de App Service se pueden supervisar fácilmente en tiempo real. En Azure Portal se representan métricas en gráficos fáciles de entender.

1. Abra [Azure Portal](https://portal.azure.com) y, después, vaya a la instancia de App Service *mywebapp\<unique_number\>* .

1. En la pestaña **Información general** se muestra información útil "de un vistazo", incluidos gráficos con métricas recientes.

    ![Captura de pantalla en la que se muestra el panel Información general](./media/monitoring/overview.png)

    * **Http 5xx**: recuento de errores del lado servidor, normalmente excepciones en el código de ASP.NET Core.
    * **Entrada de datos**: entrada de datos en la aplicación web.
    * **Salida de datos**: salida de datos de la aplicación web a los clientes.
    * **Solicitud**: recuento de solicitudes HTTP.
    * **Tiempo promedio de respuesta**: tiempo promedio de respuesta de la aplicación web a las solicitudes HTTP.

    En esta página también se encuentran varias herramientas de autoservicio para la solución de problemas y la optimización.

    ![Captura de pantalla en la que se muestran las herramientas de autoservicio](./media/monitoring/wizards.png)

    * **Diagnosticar y solucionar problemas** es un solucionador de problemas de autoservicio.
    * **Application Insights** sirve para generar perfiles de rendimiento y comportamiento de la aplicación, y se describe más adelante en esta sección.
    * **App Service Advisor** realiza recomendaciones para optimizar la experiencia de la aplicación.

## <a name="advanced-monitoring"></a>Supervisión avanzada

[Azure Monitor](/azure/monitoring-and-diagnostics/) es el servicio centralizado para supervisar todas las métricas y establecer alertas en los servicios de Azure. En Azure Monitor, los administradores pueden realizar un seguimiento pormenorizado del rendimiento e identificar tendencias. Cada servicio de Azure ofrece su propio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para Azure Monitor.

## <a name="profile-with-application-insights"></a>Perfil con Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) es un servicio de Azure para analizar el rendimiento y la estabilidad de las aplicaciones web y cómo las utilizan los usuarios. Los datos de Application Insights son más amplios y profundos que los de Azure Monitor. Los datos pueden proporcionar a desarrolladores y administradores información clave para mejorar las aplicaciones. Application Insights se puede agregar a un recurso de Azure App Service sin cambios del código.

1. Abra [Azure Portal](https://portal.azure.com) y, después, vaya a la instancia de App Service *mywebapp\<unique_number\>* .
1. En la pestaña **Información general**, haga clic en el icono **Application Insights**.

    ![Icono de Application Insights](./media/monitoring/app-insights.png)

1. Seleccione el botón de radio **Crear nuevo recurso**. Use el nombre de recurso predeterminado y seleccione la ubicación del recurso de Application Insights. No es necesario que la ubicación coincida con la de la aplicación web.

    ![Configuración de Application Insights](./media/monitoring/new-app-insights.png)

1. En **Entorno de ejecución/Marco**, seleccione **ASP.NET Core**. Acepte la configuración predeterminada.
1. Seleccione **Aceptar**. Si se le solicita confirmación, seleccione **Continuar**.
1. Una vez que se haya creado el recurso, haga clic en el nombre del recurso de Application Insights para ir directamente a la página Application Insights.

    ![Nuevo recurso de Application Insights, a punto](./media/monitoring/new-app-insights-done.png)

Mientras se usa la aplicación, los datos se acumulan. Seleccione **Actualizar** para volver a cargar la hoja con datos nuevos.

![Pestaña Información general de Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights proporciona información útil del lado servidor sin configuración adicional. Para obtener el máximo valor de Application Insights, [instrumente la aplicación con el SDK de Application Insights](/azure/application-insights/app-insights-asp-net-core). Si se configura correctamente, el servicio proporciona supervisión de un extremo a otro en el servidor web y el explorador, incluido el rendimiento del lado cliente. Para más información, vea la [documentación de Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registro

Los registros del servidor web y de la aplicación están deshabilitados de forma predeterminada en Azure App Service. Siga estos pasos para habilitar los registros:

1. Abra [Azure Portal](https://portal.azure.com) y vaya a la instancia de App Service *mywebapp\<unique_number\>* .
1. En el menú de la izquierda, desplácese hacia abajo hasta la sección **Supervisión**. Seleccione **Registros de diagnóstico**.

    ![Vínculo Registros de diagnóstico](./media/monitoring/logging.png)

1. Active **Registro de la aplicación (sistema de archivos)** . Si se le solicita, haga clic en el cuadro para instalar las extensiones para habilitar el registro de la aplicación en la aplicación web.
1. Establezca **Registro del servidor web** en **Sistema de archivos**.
1. Escriba el **Período de retención** en días. Por ejemplo, puede ser de 30.
1. Haga clic en **Guardar**.

Los registros de ASP.NET Core y del servidor web (App Service) se generan para la aplicación web. Se pueden descargar mediante la información de FTP/FTPS que se muestra. La contraseña es la misma que las credenciales de implementación que se han creado antes en esta guía. Los registros se pueden [transmitir directamente al equipo local con PowerShell o la CLI de Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). Los registros también [se pueden ver en Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Secuencias de registro

Los registros del servidor web y de la aplicación se pueden transmitir en tiempo real a través del portal.

1. Abra [Azure Portal](https://portal.azure.com) y vaya a la instancia de App Service *mywebapp\<unique_number\>* .
1. En el menú de la izquierda, desplácese hacia abajo hasta la sección **Supervisión** y seleccione **Secuencia de registro**.

    ![Captura de pantalla en la que se muestra el vínculo Secuencia de registro](./media/monitoring/log-stream.png)

Los registros también se pueden [transmitir a través de la CLI de Azure o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluido Cloud Shell.

## <a name="alerts"></a>Alertas

Azure Monitor también proporciona [alertas en tiempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) basadas en métricas, eventos administrativos y otros criterios.

> *Nota: Actualmente, las alertas sobre métricas de la aplicación web solo están disponibles en el servicio Alertas (clásico).*

El [servicio Alertas (clásico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) se puede encontrar en Azure Monitor o en la sección **Supervisión** de la configuración de App Service.

![Vínculo Alertas (clásico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Depuración en directo

Azure App Service se puede [depurar de forma remota con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) si los registros no proporcionan información suficiente. Sin embargo, la depuración remota requiere que la aplicación se compile con símbolos de depuración. La depuración no se debe realizar en producción, excepto como último recurso.

## <a name="conclusion"></a>Conclusión

En esta sección ha completado las tareas siguientes:

* Buscar datos básicos de supervisión y solución de problemas en Azure Portal
* Obtener información sobre cómo Azure Monitor proporciona una visión más profunda de las métricas en todos los servicios de Azure
* Conectar la aplicación web con Application Insights para la generación de perfiles de aplicaciones
* Activar el registro y saber dónde descargar registros
* Streaming de registros en tiempo real
* Obtener información sobre dónde configurar alertas
* Obtener información sobre la depuración remota de aplicaciones web de Azure App Service

## <a name="additional-reading"></a>Lecturas adicionales

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Supervisión del rendimiento de aplicaciones web de Azure con Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Habilitar el registro de diagnósticos para las aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Creación de alertas de métricas clásicas en Azure Monitor para servicios de Azure: Azure Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
