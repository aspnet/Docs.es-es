---
title: 'Supervisión y depuración: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Supervisión y depuración del código como parte de una solución de DevOps con ASP.NET Core y Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056756"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="321c7-103">Supervisión y depuración</span><span class="sxs-lookup"><span data-stu-id="321c7-103">Monitor and debug</span></span>

<span data-ttu-id="321c7-104">Una vez implementada la aplicación y después de compilar una canalización de DevOps, es importante comprender cómo supervisar y solucionar problemas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="321c7-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="321c7-105">En esta sección, completará las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="321c7-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="321c7-106">Buscar datos básicos de supervisión y solución de problemas en Azure Portal</span><span class="sxs-lookup"><span data-stu-id="321c7-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="321c7-107">Obtener información sobre cómo Azure Monitor proporciona una visión más profunda de las métricas en todos los servicios de Azure</span><span class="sxs-lookup"><span data-stu-id="321c7-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="321c7-108">Conectar la aplicación web con Application Insights para la generación de perfiles de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="321c7-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="321c7-109">Activar el registro y saber dónde descargar registros</span><span class="sxs-lookup"><span data-stu-id="321c7-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="321c7-110">Streaming de registros en tiempo real</span><span class="sxs-lookup"><span data-stu-id="321c7-110">Stream logs in real time</span></span>
* <span data-ttu-id="321c7-111">Obtener información sobre dónde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="321c7-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="321c7-112">Obtener información sobre la depuración remota de aplicaciones web de Azure App Service</span><span class="sxs-lookup"><span data-stu-id="321c7-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="321c7-113">Supervisión básica y solución de problemas</span><span class="sxs-lookup"><span data-stu-id="321c7-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="321c7-114">Las aplicaciones web de App Service se pueden supervisar fácilmente en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="321c7-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="321c7-115">En Azure Portal se representan métricas en gráficos fáciles de entender.</span><span class="sxs-lookup"><span data-stu-id="321c7-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="321c7-116">Abra [Azure Portal](https://portal.azure.com) y, después, vaya a la instancia de App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="321c7-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="321c7-117">En la pestaña **Información general** se muestra información útil "de un vistazo", incluidos gráficos con métricas recientes.</span><span class="sxs-lookup"><span data-stu-id="321c7-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Captura de pantalla en la que se muestra el panel Información general](./media/monitoring/overview.png)

    * <span data-ttu-id="321c7-119">**Http 5xx** : recuento de errores del lado servidor, normalmente excepciones en el código de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="321c7-119">**Http 5xx** : Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="321c7-120">**Entrada de datos** : entrada de datos en la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="321c7-120">**Data In** : Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="321c7-121">**Salida de datos** : salida de datos de la aplicación web a los clientes.</span><span class="sxs-lookup"><span data-stu-id="321c7-121">**Data Out** : Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="321c7-122">**Solicitud** : recuento de solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="321c7-122">**Requests** : Count of HTTP requests.</span></span>
    * <span data-ttu-id="321c7-123">**Tiempo promedio de respuesta** : tiempo promedio de respuesta de la aplicación web a las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="321c7-123">**Average Response Time** : Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="321c7-124">En esta página también se encuentran varias herramientas de autoservicio para la solución de problemas y la optimización.</span><span class="sxs-lookup"><span data-stu-id="321c7-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Captura de pantalla en la que se muestran las herramientas de autoservicio](./media/monitoring/wizards.png)

    * <span data-ttu-id="321c7-126">**Diagnosticar y solucionar problemas** es un solucionador de problemas de autoservicio.</span><span class="sxs-lookup"><span data-stu-id="321c7-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="321c7-127">**Application Insights** sirve para generar perfiles de rendimiento y comportamiento de la aplicación, y se describe más adelante en esta sección.</span><span class="sxs-lookup"><span data-stu-id="321c7-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="321c7-128">**App Service Advisor** realiza recomendaciones para optimizar la experiencia de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="321c7-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="321c7-129">Supervisión avanzada</span><span class="sxs-lookup"><span data-stu-id="321c7-129">Advanced monitoring</span></span>

<span data-ttu-id="321c7-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) es el servicio centralizado para supervisar todas las métricas y establecer alertas en los servicios de Azure.</span><span class="sxs-lookup"><span data-stu-id="321c7-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="321c7-131">En Azure Monitor, los administradores pueden realizar un seguimiento pormenorizado del rendimiento e identificar tendencias.</span><span class="sxs-lookup"><span data-stu-id="321c7-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="321c7-132">Cada servicio de Azure ofrece su propio [conjunto de métricas](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) para Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="321c7-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="321c7-133">Perfil con Application Insights</span><span class="sxs-lookup"><span data-stu-id="321c7-133">Profile with Application Insights</span></span>

<span data-ttu-id="321c7-134">[Application Insights](/azure/application-insights/app-insights-overview) es un servicio de Azure para analizar el rendimiento y la estabilidad de las aplicaciones web y cómo las utilizan los usuarios.</span><span class="sxs-lookup"><span data-stu-id="321c7-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="321c7-135">Los datos de Application Insights son más amplios y profundos que los de Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="321c7-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="321c7-136">Los datos pueden proporcionar a desarrolladores y administradores información clave para mejorar las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="321c7-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="321c7-137">Application Insights se puede agregar a un recurso de Azure App Service sin cambios del código.</span><span class="sxs-lookup"><span data-stu-id="321c7-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="321c7-138">Abra [Azure Portal](https://portal.azure.com) y, después, vaya a la instancia de App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="321c7-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="321c7-139">En la pestaña **Información general** , haga clic en el icono **Application Insights**.</span><span class="sxs-lookup"><span data-stu-id="321c7-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Icono de Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="321c7-141">Seleccione el botón de radio **Crear nuevo recurso**.</span><span class="sxs-lookup"><span data-stu-id="321c7-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="321c7-142">Use el nombre de recurso predeterminado y seleccione la ubicación del recurso de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="321c7-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="321c7-143">No es necesario que la ubicación coincida con la de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="321c7-143">The location doesn't need to match that of your web app.</span></span>

    ![Configuración de Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="321c7-145">En **Entorno de ejecución/Marco** , seleccione **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="321c7-145">For **Runtime/Framework** , select **ASP.NET Core**.</span></span> <span data-ttu-id="321c7-146">Acepte la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="321c7-146">Accept the default settings.</span></span>
1. <span data-ttu-id="321c7-147">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="321c7-147">Select **OK**.</span></span> <span data-ttu-id="321c7-148">Si se le solicita confirmación, seleccione **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="321c7-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="321c7-149">Una vez que se haya creado el recurso, haga clic en el nombre del recurso de Application Insights para ir directamente a la página Application Insights.</span><span class="sxs-lookup"><span data-stu-id="321c7-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nuevo recurso de Application Insights, a punto](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="321c7-151">Mientras se usa la aplicación, los datos se acumulan.</span><span class="sxs-lookup"><span data-stu-id="321c7-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="321c7-152">Seleccione **Actualizar** para volver a cargar la hoja con datos nuevos.</span><span class="sxs-lookup"><span data-stu-id="321c7-152">Select **Refresh** to reload the blade with new data.</span></span>

![Pestaña Información general de Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="321c7-154">Application Insights proporciona información útil del lado servidor sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="321c7-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="321c7-155">Para obtener el máximo valor de Application Insights, [instrumente la aplicación con el SDK de Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="321c7-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="321c7-156">Si se configura correctamente, el servicio proporciona supervisión de un extremo a otro en el servidor web y el explorador, incluido el rendimiento del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="321c7-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="321c7-157">Para más información, vea la [documentación de Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="321c7-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="321c7-158">Registro</span><span class="sxs-lookup"><span data-stu-id="321c7-158">Logging</span></span>

<span data-ttu-id="321c7-159">Los registros del servidor web y de la aplicación están deshabilitados de forma predeterminada en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="321c7-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="321c7-160">Siga estos pasos para habilitar los registros:</span><span class="sxs-lookup"><span data-stu-id="321c7-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="321c7-161">Abra [Azure Portal](https://portal.azure.com) y vaya a la instancia de App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="321c7-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="321c7-162">En el menú de la izquierda, desplácese hacia abajo hasta la sección **Supervisión**.</span><span class="sxs-lookup"><span data-stu-id="321c7-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="321c7-163">Seleccione **Registros de diagnóstico**.</span><span class="sxs-lookup"><span data-stu-id="321c7-163">Select **Diagnostics logs**.</span></span>

    ![Vínculo Registros de diagnóstico](./media/monitoring/logging.png)

1. <span data-ttu-id="321c7-165">Active **Registro de la aplicación (sistema de archivos)** .</span><span class="sxs-lookup"><span data-stu-id="321c7-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="321c7-166">Si se le solicita, haga clic en el cuadro para instalar las extensiones para habilitar el registro de la aplicación en la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="321c7-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="321c7-167">Establezca **Registro del servidor web** en **Sistema de archivos**.</span><span class="sxs-lookup"><span data-stu-id="321c7-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="321c7-168">Escriba el **Período de retención** en días.</span><span class="sxs-lookup"><span data-stu-id="321c7-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="321c7-169">Por ejemplo, puede ser de 30.</span><span class="sxs-lookup"><span data-stu-id="321c7-169">For example, 30.</span></span>
1. <span data-ttu-id="321c7-170">Haga clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="321c7-170">Click **Save**.</span></span>

<span data-ttu-id="321c7-171">Los registros de ASP.NET Core y del servidor web (App Service) se generan para la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="321c7-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="321c7-172">Se pueden descargar mediante la información de FTP/FTPS que se muestra.</span><span class="sxs-lookup"><span data-stu-id="321c7-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="321c7-173">La contraseña es la misma que las credenciales de implementación que se han creado antes en esta guía.</span><span class="sxs-lookup"><span data-stu-id="321c7-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="321c7-174">Los registros se pueden [transmitir directamente al equipo local con PowerShell o la CLI de Azure](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="321c7-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="321c7-175">Los registros también [se pueden ver en Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="321c7-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="321c7-176">Secuencias de registro</span><span class="sxs-lookup"><span data-stu-id="321c7-176">Log streaming</span></span>

<span data-ttu-id="321c7-177">Los registros del servidor web y de la aplicación se pueden transmitir en tiempo real a través del portal.</span><span class="sxs-lookup"><span data-stu-id="321c7-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="321c7-178">Abra [Azure Portal](https://portal.azure.com) y vaya a la instancia de App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="321c7-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="321c7-179">En el menú de la izquierda, desplácese hacia abajo hasta la sección **Supervisión** y seleccione **Secuencia de registro**.</span><span class="sxs-lookup"><span data-stu-id="321c7-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Captura de pantalla en la que se muestra el vínculo Secuencia de registro](./media/monitoring/log-stream.png)

<span data-ttu-id="321c7-181">Los registros también se pueden [transmitir a través de la CLI de Azure o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), incluido Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="321c7-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="321c7-182">Alertas</span><span class="sxs-lookup"><span data-stu-id="321c7-182">Alerts</span></span>

<span data-ttu-id="321c7-183">Azure Monitor también proporciona [alertas en tiempo real](/azure/monitoring-and-diagnostics/insights-alerts-portal) basadas en métricas, eventos administrativos y otros criterios.</span><span class="sxs-lookup"><span data-stu-id="321c7-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="321c7-184">*Nota: Actualmente, las alertas sobre métricas de la aplicación web solo están disponibles en el servicio Alertas (clásico).*</span><span class="sxs-lookup"><span data-stu-id="321c7-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="321c7-185">El [servicio Alertas (clásico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) se puede encontrar en Azure Monitor o en la sección **Supervisión** de la configuración de App Service.</span><span class="sxs-lookup"><span data-stu-id="321c7-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Vínculo Alertas (clásico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="321c7-187">Depuración en directo</span><span class="sxs-lookup"><span data-stu-id="321c7-187">Live debugging</span></span>

<span data-ttu-id="321c7-188">Azure App Service se puede [depurar de forma remota con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) si los registros no proporcionan información suficiente.</span><span class="sxs-lookup"><span data-stu-id="321c7-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="321c7-189">Sin embargo, la depuración remota requiere que la aplicación se compile con símbolos de depuración.</span><span class="sxs-lookup"><span data-stu-id="321c7-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="321c7-190">La depuración no se debe realizar en producción, excepto como último recurso.</span><span class="sxs-lookup"><span data-stu-id="321c7-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="321c7-191">Conclusión</span><span class="sxs-lookup"><span data-stu-id="321c7-191">Conclusion</span></span>

<span data-ttu-id="321c7-192">En esta sección ha completado las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="321c7-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="321c7-193">Buscar datos básicos de supervisión y solución de problemas en Azure Portal</span><span class="sxs-lookup"><span data-stu-id="321c7-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="321c7-194">Obtener información sobre cómo Azure Monitor proporciona una visión más profunda de las métricas en todos los servicios de Azure</span><span class="sxs-lookup"><span data-stu-id="321c7-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="321c7-195">Conectar la aplicación web con Application Insights para la generación de perfiles de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="321c7-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="321c7-196">Activar el registro y saber dónde descargar registros</span><span class="sxs-lookup"><span data-stu-id="321c7-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="321c7-197">Streaming de registros en tiempo real</span><span class="sxs-lookup"><span data-stu-id="321c7-197">Stream logs in real time</span></span>
* <span data-ttu-id="321c7-198">Obtener información sobre dónde configurar alertas</span><span class="sxs-lookup"><span data-stu-id="321c7-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="321c7-199">Obtener información sobre la depuración remota de aplicaciones web de Azure App Service</span><span class="sxs-lookup"><span data-stu-id="321c7-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="321c7-200">Lecturas adicionales</span><span class="sxs-lookup"><span data-stu-id="321c7-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="321c7-201">Supervisión del rendimiento de aplicaciones web de Azure con Application Insights</span><span class="sxs-lookup"><span data-stu-id="321c7-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="321c7-202">Habilitar el registro de diagnósticos para las aplicaciones web en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="321c7-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="321c7-203">Solución de problemas de una aplicación web en Azure App Service con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="321c7-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="321c7-204">Creación de alertas de métricas clásicas en Azure Monitor para servicios de Azure: Azure Portal</span><span class="sxs-lookup"><span data-stu-id="321c7-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
