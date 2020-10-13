---
title: Hospedaje fuera de proceso con IIS y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el hospedaje fuera de proceso con IIS y el módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755213"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="d7072-103">Hospedaje fuera de proceso con IIS y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7072-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="d7072-104">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo ASP.NET Core se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="d7072-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="d7072-105">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea.</span><span class="sxs-lookup"><span data-stu-id="d7072-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d7072-106">Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="d7072-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d7072-107">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:</span><span class="sxs-lookup"><span data-stu-id="d7072-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo ASP.NET Core en el escenario de hospedaje fuera de proceso](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="d7072-109">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="d7072-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="d7072-110">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web,</span><span class="sxs-lookup"><span data-stu-id="d7072-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="d7072-111">que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d7072-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="d7072-112">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación,</span><span class="sxs-lookup"><span data-stu-id="d7072-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="d7072-113">que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="d7072-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="d7072-114">El módulo ASP.NET Core especifica el puerto a través de una variable de entorno en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d7072-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="d7072-115">La extensión de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> configura el servidor para que escuche en `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="d7072-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="d7072-116">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="d7072-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d7072-117">El módulo no es compatible con el reenvío de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7072-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="d7072-118">Las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7072-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d7072-119">Una vez que Kestrel toma la solicitud del módulo, la reenvía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7072-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d7072-120">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d7072-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d7072-121">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7072-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d7072-122">La respuesta de la aplicación se vuelve a pasar a IIS, que la reenvía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d7072-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d7072-123">Para obtener instrucciones sobre la configuración del módulo ASP.NET Core, vea <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="d7072-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d7072-124">Para obtener más información sobre el hospedaje, consulte [Hospedaje en ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d7072-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d7072-125">Configuración de aplicación</span><span class="sxs-lookup"><span data-stu-id="d7072-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="d7072-126">Habilitación de los componentes de integración con IIS</span><span class="sxs-lookup"><span data-stu-id="d7072-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="d7072-127">Al compilar un host en `CreateHostBuilder` (`Program.cs`), llame a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para permitir la integración de IIS:</span><span class="sxs-lookup"><span data-stu-id="d7072-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="d7072-128">Para más información sobre `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="d7072-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="d7072-129">**Modelo de hospedaje fuera de proceso**</span><span class="sxs-lookup"><span data-stu-id="d7072-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="d7072-130">Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="d7072-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="d7072-131">En el ejemplo siguiente se impide que la aplicación rellene `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="d7072-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="d7072-132">Opción</span><span class="sxs-lookup"><span data-stu-id="d7072-132">Option</span></span>                         | <span data-ttu-id="d7072-133">Default</span><span class="sxs-lookup"><span data-stu-id="d7072-133">Default</span></span> | <span data-ttu-id="d7072-134">Parámetro</span><span class="sxs-lookup"><span data-stu-id="d7072-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d7072-135">Si es `true`, el [middleware de integración con IIS](#enable-the-iisintegration-components) establece el `HttpContext.User` autenticado mediante [autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d7072-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d7072-136">Si es `false`, el middleware solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="d7072-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d7072-137">Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="d7072-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d7072-138">Para más información, consulte el tema [Autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="d7072-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d7072-139">Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="d7072-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="d7072-140">Si `HttpContext.Connection.ClientCertificate` y el encabezado de solicitud `true` está presente, se rellena `MS-ASPNETCORE-CLIENTCERT`.</span><span class="sxs-lookup"><span data-stu-id="d7072-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d7072-141">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="d7072-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d7072-142">El [middleware de integración de IIS](#enable-the-iisintegration-components) y el módulo de ASP.NET Core están configurados para reenviar:</span><span class="sxs-lookup"><span data-stu-id="d7072-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="d7072-143">Esquema (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d7072-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="d7072-144">Dirección IP remota donde se originó la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d7072-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="d7072-145">El [middleware de integración de IIS](#enable-the-iisintegration-components) configura el middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d7072-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="d7072-146">Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales.</span><span class="sxs-lookup"><span data-stu-id="d7072-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d7072-147">Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="d7072-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d7072-148">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="d7072-148">Out-of-process hosting model</span></span>

<span data-ttu-id="d7072-149">Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( `.csproj`):</span><span class="sxs-lookup"><span data-stu-id="d7072-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="d7072-150">El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d7072-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="d7072-151">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="d7072-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="d7072-152">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="d7072-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="d7072-153">Fuera del proceso, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> para:</span><span class="sxs-lookup"><span data-stu-id="d7072-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="d7072-154">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7072-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="d7072-155">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="d7072-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="d7072-156">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="d7072-156">Process name</span></span>

<span data-ttu-id="d7072-157">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="d7072-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="d7072-158">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="d7072-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="d7072-159">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="d7072-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="d7072-160">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="d7072-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="d7072-161">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="d7072-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="d7072-162">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="d7072-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="d7072-163">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="d7072-163">Forward Windows authentication tokens.</span></span>
