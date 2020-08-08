---
title: Registro y diagnósticos en ASP.NET CoreSignalR
author: anurse
description: Obtenga información sobre cómo recopilar diagnósticos desde la SignalR aplicación ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 61733fe4fbcd7b94662404a39a288ff2ce75ec53
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021840"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="3c59c-103">Registro y diagnósticos en ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="3c59c-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="3c59c-104">Por [Andrew Stanton-enfermera](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3c59c-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="3c59c-105">En este artículo se proporcionan instrucciones para recopilar diagnósticos de la SignalR aplicación ASP.net Core para ayudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="3c59c-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="3c59c-106">Registro del lado servidor</span><span class="sxs-lookup"><span data-stu-id="3c59c-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c59c-107">Los registros del lado servidor pueden contener información confidencial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c59c-108">**Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.</span><span class="sxs-lookup"><span data-stu-id="3c59c-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c59c-109">Dado que SignalR forma parte de ASP.net Core, utiliza el sistema de registro de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="3c59c-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="3c59c-110">En la configuración predeterminada, SignalR registra muy poca información, pero esto puede configurarse.</span><span class="sxs-lookup"><span data-stu-id="3c59c-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="3c59c-111">Vea la documentación sobre el [registro de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obtener más información sobre la configuración del registro de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c59c-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="3c59c-112">SignalRutiliza dos categorías de registrador:</span><span class="sxs-lookup"><span data-stu-id="3c59c-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="3c59c-113">`Microsoft.AspNetCore.SignalR`: Para los registros relacionados con los protocolos del concentrador, la activación de los concentradores, la invocación de métodos y otras actividades relacionadas con el concentrador.</span><span class="sxs-lookup"><span data-stu-id="3c59c-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="3c59c-114">`Microsoft.AspNetCore.Http.Connections`: Para registros relacionados con transportes, como WebSockets, sondeos largos, eventos enviados por el servidor e infraestructura de bajo nivel SignalR .</span><span class="sxs-lookup"><span data-stu-id="3c59c-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="3c59c-115">Para habilitar los registros detallados de SignalR , configure los dos prefijos anteriores en el `Debug` nivel del *appsettings.jsen* el archivo agregando los siguientes elementos a la `LogLevel` subsección de `Logging` :</span><span class="sxs-lookup"><span data-stu-id="3c59c-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="3c59c-116">También puede configurarlo en el código del `CreateWebHostBuilder` método:</span><span class="sxs-lookup"><span data-stu-id="3c59c-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="3c59c-117">Si no está usando la configuración basada en JSON, establezca los siguientes valores de configuración en el sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="3c59c-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="3c59c-118">Consulte la documentación del sistema de configuración para determinar cómo especificar valores de configuración anidados.</span><span class="sxs-lookup"><span data-stu-id="3c59c-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="3c59c-119">Por ejemplo, al usar variables de entorno, se usan dos caracteres `_` en lugar de `:` (por ejemplo, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="3c59c-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="3c59c-120">Se recomienda usar el nivel `Debug` al recopilar diagnósticos más detallados para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="3c59c-121">El nivel `Trace` genera diagnósticos de nivel muy bajo y rara vez es necesario para diagnosticar problemas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="3c59c-122">Acceso a los registros del lado servidor</span><span class="sxs-lookup"><span data-stu-id="3c59c-122">Access server-side logs</span></span>

<span data-ttu-id="3c59c-123">La forma de acceder a los registros del lado servidor depende del entorno que se ejecute.</span><span class="sxs-lookup"><span data-stu-id="3c59c-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="3c59c-124">Como aplicación de consola fuera de IIS</span><span class="sxs-lookup"><span data-stu-id="3c59c-124">As a console app outside IIS</span></span>

<span data-ttu-id="3c59c-125">Si se ejecuta en una aplicación de consola, el [registrador de la consola](xref:fundamentals/logging/index#console) debe estar habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3c59c-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="3c59c-126">SignalRlos registros aparecerán en la consola de.</span><span class="sxs-lookup"><span data-stu-id="3c59c-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="3c59c-127">Dentro de IIS Express desde Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c59c-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="3c59c-128">Visual Studio muestra la salida del registro en la ventana de **salida** .</span><span class="sxs-lookup"><span data-stu-id="3c59c-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="3c59c-129">Seleccione la opción desplegable **servidor Web de ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="3c59c-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="3c59c-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3c59c-130">Azure App Service</span></span>

<span data-ttu-id="3c59c-131">Habilite la opción de **registro de aplicaciones (sistema de archivos)** en la sección **registros de diagnóstico** del portal de Azure App Service y configure el **nivel** en `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="3c59c-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="3c59c-132">Los registros deben estar disponibles en el servicio de **streaming de registros** y en los registros del sistema de archivos del App Service.</span><span class="sxs-lookup"><span data-stu-id="3c59c-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="3c59c-133">Para más información, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="3c59c-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="3c59c-134">Otros entornos</span><span class="sxs-lookup"><span data-stu-id="3c59c-134">Other environments</span></span>

<span data-ttu-id="3c59c-135">Si la aplicación se implementa en otro entorno (por ejemplo, Docker, Kubernetes o un servicio de Windows), vea <xref:fundamentals/logging/index> para obtener más información sobre cómo configurar los proveedores de registro adecuados para el entorno.</span><span class="sxs-lookup"><span data-stu-id="3c59c-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="3c59c-136">Registro de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="3c59c-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c59c-137">Los registros del lado cliente pueden contener información confidencial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c59c-138">**Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.</span><span class="sxs-lookup"><span data-stu-id="3c59c-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c59c-139">Al utilizar el cliente de JavaScript, puede configurar las opciones de registro mediante el `configureLogging` método de `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="3c59c-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="3c59c-140">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="3c59c-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="3c59c-141">En la tabla siguiente se muestran los niveles de registro disponibles para el cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3c59c-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="3c59c-142">Al establecer el nivel de registro en uno de estos valores, se habilita el registro en ese nivel y en todos los niveles situados por encima de él en la tabla.</span><span class="sxs-lookup"><span data-stu-id="3c59c-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="3c59c-143">Nivel</span><span class="sxs-lookup"><span data-stu-id="3c59c-143">Level</span></span> | <span data-ttu-id="3c59c-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="3c59c-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="3c59c-145">No se registra ningún mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c59c-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="3c59c-146">Mensajes que indican un error en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="3c59c-147">Mensajes que indican un error en la operación actual.</span><span class="sxs-lookup"><span data-stu-id="3c59c-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="3c59c-148">Mensajes que indican un problema no grave.</span><span class="sxs-lookup"><span data-stu-id="3c59c-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="3c59c-149">Mensajes informativos.</span><span class="sxs-lookup"><span data-stu-id="3c59c-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="3c59c-150">Mensajes de diagnóstico útiles para la depuración.</span><span class="sxs-lookup"><span data-stu-id="3c59c-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="3c59c-151">Mensajes de diagnóstico muy detallados diseñados para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="3c59c-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="3c59c-152">Una vez que haya configurado el nivel de detalle, los registros se escribirán en la consola del explorador (o la salida estándar en una aplicación de NodeJS).</span><span class="sxs-lookup"><span data-stu-id="3c59c-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="3c59c-153">Si desea enviar registros a un sistema de registro personalizado, puede proporcionar un objeto de JavaScript que implemente la `ILogger` interfaz.</span><span class="sxs-lookup"><span data-stu-id="3c59c-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="3c59c-154">El único método que debe implementarse es `log` , que toma el nivel del evento y el mensaje asociado al evento.</span><span class="sxs-lookup"><span data-stu-id="3c59c-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="3c59c-155">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3c59c-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="3c59c-156">Registro de clientes de  .NET</span><span class="sxs-lookup"><span data-stu-id="3c59c-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3c59c-157">Los registros del lado cliente pueden contener información confidencial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3c59c-158">**Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.</span><span class="sxs-lookup"><span data-stu-id="3c59c-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c59c-159">Para obtener los registros del cliente .NET, puede usar el `ConfigureLogging` método en `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3c59c-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="3c59c-160">Esto funciona de la misma manera que el `ConfigureLogging` método en `WebHostBuilder` y `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3c59c-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="3c59c-161">Puede configurar los mismos proveedores de registro que utiliza en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c59c-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="3c59c-162">Sin embargo, tiene que instalar y habilitar manualmente los paquetes de NuGet para los proveedores de registro individuales.</span><span class="sxs-lookup"><span data-stu-id="3c59c-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="3c59c-163">Para agregar el registro de cliente .NET a una Blazor WebAssembly aplicación, vea <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .</span><span class="sxs-lookup"><span data-stu-id="3c59c-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="3c59c-164">Registro de consolas</span><span class="sxs-lookup"><span data-stu-id="3c59c-164">Console logging</span></span>

<span data-ttu-id="3c59c-165">Para habilitar el registro de la consola, agregue el paquete [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="3c59c-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="3c59c-166">A continuación, use el `AddConsole` método para configurar el registrador de la consola:</span><span class="sxs-lookup"><span data-stu-id="3c59c-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="3c59c-167">Registro de la ventana de salida de depuración</span><span class="sxs-lookup"><span data-stu-id="3c59c-167">Debug output window logging</span></span>

<span data-ttu-id="3c59c-168">También puede configurar los registros para ir a la ventana de **salida** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3c59c-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="3c59c-169">Instale el paquete [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) y use el `AddDebug` método:</span><span class="sxs-lookup"><span data-stu-id="3c59c-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="3c59c-170">Otros proveedores de registro</span><span class="sxs-lookup"><span data-stu-id="3c59c-170">Other logging providers</span></span>

<span data-ttu-id="3c59c-171">SignalRadmite otros proveedores de registro, como Serilog, SEQ, NLog o cualquier otro sistema de registro que se integre con `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="3c59c-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="3c59c-172">Si el sistema de registro proporciona un `ILoggerProvider` , puede registrarlo con `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="3c59c-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="3c59c-173">Nivel de detalle del control</span><span class="sxs-lookup"><span data-stu-id="3c59c-173">Control verbosity</span></span>

<span data-ttu-id="3c59c-174">Si está registrando desde otros lugares de la aplicación, cambiar el nivel predeterminado a `Debug` puede ser demasiado detallado.</span><span class="sxs-lookup"><span data-stu-id="3c59c-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="3c59c-175">Puede usar un filtro para configurar el nivel de registro de los SignalR registros.</span><span class="sxs-lookup"><span data-stu-id="3c59c-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="3c59c-176">Esto puede hacerse en el código, de la misma manera que en el servidor:</span><span class="sxs-lookup"><span data-stu-id="3c59c-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="3c59c-177">Seguimientos de red</span><span class="sxs-lookup"><span data-stu-id="3c59c-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="3c59c-178">Un seguimiento de red contiene todo el contenido de todos los mensajes enviados por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c59c-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="3c59c-179">No exponga **nunca** los seguimientos de red sin procesar de las aplicaciones de producción en foros públicos como github.</span><span class="sxs-lookup"><span data-stu-id="3c59c-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3c59c-180">Si se produce un problema, un seguimiento de red a veces puede proporcionar mucha información útil.</span><span class="sxs-lookup"><span data-stu-id="3c59c-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="3c59c-181">Esto es especialmente útil si va a archivar un problema en nuestro seguimiento de problemas.</span><span class="sxs-lookup"><span data-stu-id="3c59c-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="3c59c-182">Recopilar un seguimiento de red con Fiddler (opción preferida)</span><span class="sxs-lookup"><span data-stu-id="3c59c-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="3c59c-183">Este método funciona para todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3c59c-183">This method works for all apps.</span></span>

<span data-ttu-id="3c59c-184">Fiddler es una herramienta muy eficaz para la recopilación de seguimientos HTTP.</span><span class="sxs-lookup"><span data-stu-id="3c59c-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="3c59c-185">Instálelo desde [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inícielo y, a continuación, ejecute la aplicación y reproduzca el problema.</span><span class="sxs-lookup"><span data-stu-id="3c59c-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="3c59c-186">Fiddler está disponible para Windows y hay versiones beta para macOS y Linux.</span><span class="sxs-lookup"><span data-stu-id="3c59c-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="3c59c-187">Si se conecta mediante HTTPS, hay algunos pasos adicionales para asegurarse de que Fiddler puede descifrar el tráfico HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3c59c-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="3c59c-188">Para obtener más información, consulte la [documentación de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="3c59c-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="3c59c-189">Una vez que haya recopilado el seguimiento, puede exportar el seguimiento eligiendo **archivo**  >  **Guardar**  >  **todas las sesiones** en la barra de menús.</span><span class="sxs-lookup"><span data-stu-id="3c59c-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportar todas las sesiones de Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="3c59c-191">Recopilación de un seguimiento de red con tcpdump (solo macOS y Linux)</span><span class="sxs-lookup"><span data-stu-id="3c59c-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="3c59c-192">Este método funciona para todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3c59c-192">This method works for all apps.</span></span>

<span data-ttu-id="3c59c-193">Puede recopilar seguimientos TCP sin formato mediante tcpdump ejecutando el siguiente comando desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="3c59c-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="3c59c-194">Es posible que deba ser `root` o agregar el prefijo al comando `sudo` si obtiene un error de permisos:</span><span class="sxs-lookup"><span data-stu-id="3c59c-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="3c59c-195">Reemplazar `[interface]` por la interfaz de red en la que desea realizar la captura.</span><span class="sxs-lookup"><span data-stu-id="3c59c-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="3c59c-196">Normalmente, se trata de algo parecido a `/dev/eth0` (para la interfaz Ethernet estándar) o `/dev/lo0` (para el tráfico localhost).</span><span class="sxs-lookup"><span data-stu-id="3c59c-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="3c59c-197">Para obtener más información, consulte la `tcpdump` página Man en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="3c59c-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="3c59c-198">Recopilar un seguimiento de red en el explorador</span><span class="sxs-lookup"><span data-stu-id="3c59c-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="3c59c-199">Este método solo funciona para aplicaciones basadas en explorador.</span><span class="sxs-lookup"><span data-stu-id="3c59c-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="3c59c-200">La mayoría del explorador Herramientas de desarrollo tener una pestaña "red" que le permita capturar la actividad de red entre el explorador y el servidor.</span><span class="sxs-lookup"><span data-stu-id="3c59c-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="3c59c-201">Sin embargo, estos seguimientos no incluyen mensajes de eventos WebSocket y enviados por el servidor.</span><span class="sxs-lookup"><span data-stu-id="3c59c-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="3c59c-202">Si usa esos transportes, el mejor enfoque es usar una herramienta como Fiddler o TcpDump (descrita a continuación).</span><span class="sxs-lookup"><span data-stu-id="3c59c-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="3c59c-203">Microsoft Edge e Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="3c59c-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="3c59c-204">(Las instrucciones son las mismas para los perímetros e Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="3c59c-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="3c59c-205">Presione F12 para abrir las herramientas de desarrollo</span><span class="sxs-lookup"><span data-stu-id="3c59c-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c59c-206">Haga clic en la pestaña red.</span><span class="sxs-lookup"><span data-stu-id="3c59c-206">Click the Network Tab</span></span>
3. <span data-ttu-id="3c59c-207">Actualizar la página (si es necesario) y reproducir el problema</span><span class="sxs-lookup"><span data-stu-id="3c59c-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c59c-208">Haga clic en el icono guardar en la barra de herramientas para exportar el seguimiento como un archivo "HAR":</span><span class="sxs-lookup"><span data-stu-id="3c59c-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![El icono guardar en la pestaña red de herramientas de desarrollo de Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="3c59c-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="3c59c-210">Google Chrome</span></span>

1. <span data-ttu-id="3c59c-211">Presione F12 para abrir las herramientas de desarrollo</span><span class="sxs-lookup"><span data-stu-id="3c59c-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c59c-212">Haga clic en la pestaña red.</span><span class="sxs-lookup"><span data-stu-id="3c59c-212">Click the Network Tab</span></span>
3. <span data-ttu-id="3c59c-213">Actualizar la página (si es necesario) y reproducir el problema</span><span class="sxs-lookup"><span data-stu-id="3c59c-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c59c-214">Haga clic con el botón derecho en cualquier parte de la lista de solicitudes y elija "guardar como HAR with Content":</span><span class="sxs-lookup"><span data-stu-id="3c59c-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opción "guardar como HAR con contenido" en la pestaña red de herramientas de desarrollo de Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="3c59c-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="3c59c-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="3c59c-217">Presione F12 para abrir las herramientas de desarrollo</span><span class="sxs-lookup"><span data-stu-id="3c59c-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="3c59c-218">Haga clic en la pestaña red.</span><span class="sxs-lookup"><span data-stu-id="3c59c-218">Click the Network Tab</span></span>
3. <span data-ttu-id="3c59c-219">Actualizar la página (si es necesario) y reproducir el problema</span><span class="sxs-lookup"><span data-stu-id="3c59c-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="3c59c-220">Haga clic con el botón derecho en cualquier parte de la lista de solicitudes y elija "guardar todo como HAR"</span><span class="sxs-lookup"><span data-stu-id="3c59c-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opción "guardar todo como HAR" en Mozilla Firefox herramientas de desarrollo pestaña red](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="3c59c-222">Adjuntar archivos de diagnóstico a problemas de GitHub</span><span class="sxs-lookup"><span data-stu-id="3c59c-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="3c59c-223">Puede adjuntar archivos de diagnóstico a problemas de GitHub cambiando su nombre para que tengan una `.txt` extensión y arrastrándolos y colocándolos en el problema.</span><span class="sxs-lookup"><span data-stu-id="3c59c-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="3c59c-224">No pegue el contenido de los archivos de registro o los seguimientos de red en un problema de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3c59c-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="3c59c-225">Estos registros y seguimientos pueden ser bastante grandes y GitHub normalmente los trunca.</span><span class="sxs-lookup"><span data-stu-id="3c59c-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Arrastrar archivos de registro a un problema de GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="3c59c-227">Métricas</span><span class="sxs-lookup"><span data-stu-id="3c59c-227">Metrics</span></span>

<span data-ttu-id="3c59c-228">Métricas es una representación de medidas de datos a lo largo de intervalos de tiempo.</span><span class="sxs-lookup"><span data-stu-id="3c59c-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="3c59c-229">Por ejemplo, solicitudes por segundo.</span><span class="sxs-lookup"><span data-stu-id="3c59c-229">For example, requests per second.</span></span> <span data-ttu-id="3c59c-230">Los datos de las métricas permiten la observación del estado de una aplicación en un nivel alto.</span><span class="sxs-lookup"><span data-stu-id="3c59c-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="3c59c-231">Las métricas gRPC de .NET se emiten mediante <xref:System.Diagnostics.Tracing.EventCounter>.</span><span class="sxs-lookup"><span data-stu-id="3c59c-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="3c59c-232">SignalRmétricas de servidor</span><span class="sxs-lookup"><span data-stu-id="3c59c-232">SignalR server metrics</span></span>

<span data-ttu-id="3c59c-233">SignalRlas métricas del servidor se indican en el <xref:Microsoft.AspNetCore.Http.Connections> origen del evento.</span><span class="sxs-lookup"><span data-stu-id="3c59c-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="3c59c-234">Nombre</span><span class="sxs-lookup"><span data-stu-id="3c59c-234">Name</span></span>                    | <span data-ttu-id="3c59c-235">Descripción</span><span class="sxs-lookup"><span data-stu-id="3c59c-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="3c59c-236">Conexiones totales iniciadas</span><span class="sxs-lookup"><span data-stu-id="3c59c-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="3c59c-237">Conexiones totales detenidas</span><span class="sxs-lookup"><span data-stu-id="3c59c-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="3c59c-238">Tiempo de espera de conexiones totales agotado</span><span class="sxs-lookup"><span data-stu-id="3c59c-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="3c59c-239">Conexiones actuales</span><span class="sxs-lookup"><span data-stu-id="3c59c-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="3c59c-240">Duración media de la conexión</span><span class="sxs-lookup"><span data-stu-id="3c59c-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="3c59c-241">Observación de métricas</span><span class="sxs-lookup"><span data-stu-id="3c59c-241">Observe metrics</span></span>

<span data-ttu-id="3c59c-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) es una herramienta de supervisión de rendimiento diseñada para la investigación del rendimiento y la supervisión del estado de primer nivel ad hoc.</span><span class="sxs-lookup"><span data-stu-id="3c59c-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="3c59c-243">Supervise una aplicación .NET con `Microsoft.AspNetCore.Http.Connections` como el nombre del proveedor.</span><span class="sxs-lookup"><span data-stu-id="3c59c-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="3c59c-244">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3c59c-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="3c59c-245">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3c59c-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
