---
title: Hospedaje en proceso con IIS y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el hospedaje en proceso con IIS y el módulo de ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755204"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="75fef-103">Hospedaje en proceso con IIS y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75fef-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="75fef-104">El hospedaje en proceso ejecuta una aplicación de ASP.NET Core en el mismo proceso que su proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="75fef-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="75fef-105">El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina.</span><span class="sxs-lookup"><span data-stu-id="75fef-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="75fef-106">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada en proceso:</span><span class="sxs-lookup"><span data-stu-id="75fef-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo ASP.NET Core en el escenario de hospedaje dentro de proceso](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="75fef-108">Habilitación del hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="75fef-108">Enable in-process hosting</span></span>

<span data-ttu-id="75fef-109">A partir de ASP.NET Core 3.0, el hospedaje en proceso se ha habilitado de forma predeterminada en todas las aplicaciones implementadas en IIS.</span><span class="sxs-lookup"><span data-stu-id="75fef-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="75fef-110">Para configurar explícitamente una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `InProcess` en el archivo del proyecto (`.csproj`):</span><span class="sxs-lookup"><span data-stu-id="75fef-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="75fef-111">Arquitectura general</span><span class="sxs-lookup"><span data-stu-id="75fef-111">General architecture</span></span>

<span data-ttu-id="75fef-112">El flujo general de una solicitud es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="75fef-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="75fef-113">Una solicitud llega de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="75fef-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="75fef-114">El controlador enruta la solicitud nativa a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="75fef-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="75fef-115">El módulo ASP.NET Core recibe la solicitud nativa y la pasa a IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="75fef-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="75fef-116">El servidor HTTP de IIS es una implementación de servidor en proceso para IIS que convierte una solicitud nativa en administrada.</span><span class="sxs-lookup"><span data-stu-id="75fef-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="75fef-117">Una vez que el servidor HTTP de IIS procesa la solicitud:</span><span class="sxs-lookup"><span data-stu-id="75fef-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="75fef-118">La solicitud se envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75fef-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="75fef-119">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75fef-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="75fef-120">La respuesta de la aplicación se pasa a IIS a través del servidor HTTP de IIS.</span><span class="sxs-lookup"><span data-stu-id="75fef-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="75fef-121">IIS envía la respuesta al cliente que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="75fef-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="75fef-122">`CreateDefaultBuilder` agrega una instancia <xref:Microsoft.AspNetCore.Hosting.Server.IServer> mediante una llamada al método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para iniciar [CoreCLR](/dotnet/standard/glossary#coreclr) y hospedar la aplicación dentro del proceso de trabajo de IIS (`w3wp.exe` o `iisexpress.exe`).</span><span class="sxs-lookup"><span data-stu-id="75fef-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="75fef-123">Las pruebas de rendimiento indican que el hospedaje de una aplicación .NET Core en proceso proporciona un rendimiento de solicitud considerablemente mayor en comparación con el hospedaje de solicitudes de aplicaciones fuera de proceso y de proxy para [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="75fef-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="75fef-124">Las aplicaciones publicadas como un único archivo ejecutable no se pueden cargar por el modelo de hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="75fef-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="75fef-125">Configuración de aplicación</span><span class="sxs-lookup"><span data-stu-id="75fef-125">Application configuration</span></span>

<span data-ttu-id="75fef-126">Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="75fef-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="75fef-127">Con el ejemplo siguiente se deshabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="75fef-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="75fef-128">Opción</span><span class="sxs-lookup"><span data-stu-id="75fef-128">Option</span></span> | <span data-ttu-id="75fef-129">Default</span><span class="sxs-lookup"><span data-stu-id="75fef-129">Default</span></span> | <span data-ttu-id="75fef-130">Parámetro</span><span class="sxs-lookup"><span data-stu-id="75fef-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="75fef-131">Si es `true`, el servidor IIS establece el `HttpContext.User` autenticado mediante la [autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="75fef-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="75fef-132">Si es `false`, el servidor solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="75fef-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="75fef-133">Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione.</span><span class="sxs-lookup"><span data-stu-id="75fef-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="75fef-134">Para obtener más información, vea [Autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="75fef-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="75fef-135">Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="75fef-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="75fef-136">Si se permiten la E/S sincrónica para `HttpContext.Request` y `HttpContext.Response`.</span><span class="sxs-lookup"><span data-stu-id="75fef-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="75fef-137">Obtiene o establece el tamaño máximo del cuerpo de solicitud para `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="75fef-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="75fef-138">Tenga en cuenta que el propio IIS tiene el límite de `maxAllowedContentLength`, que se procesará antes que el valor de `MaxRequestBodySize` establecido en `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="75fef-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="75fef-139">El cambio de `MaxRequestBodySize` no afectará a `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="75fef-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="75fef-140">Para aumentar `maxAllowedContentLength`, agregue una entrada al archivo `web.config` para establecer `maxAllowedContentLength` en un valor superior.</span><span class="sxs-lookup"><span data-stu-id="75fef-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="75fef-141">Para más información, consulte [Configuración](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="75fef-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="75fef-142">Diferencias entre el hospedaje en proceso y fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="75fef-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="75fef-143">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="75fef-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="75fef-144">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="75fef-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="75fef-145">Dentro del proceso, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para:</span><span class="sxs-lookup"><span data-stu-id="75fef-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="75fef-146">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="75fef-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="75fef-147">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75fef-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="75fef-148">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="75fef-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="75fef-149">El [atributo `requestTimeout`](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="75fef-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="75fef-150">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="75fef-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="75fef-151">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="75fef-151">Use one app pool per app.</span></span>

* <span data-ttu-id="75fef-152">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="75fef-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="75fef-153">Por ejemplo, las aplicaciones publicadas para 32 bits (x86) deben tener habilitados 32 bits para los grupos de aplicaciones de IIS.</span><span class="sxs-lookup"><span data-stu-id="75fef-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="75fef-154">Para más información, consulte la sección [Creación del sitio de IIS](xref:tutorials/publish-to-iis#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="75fef-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="75fef-155">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="75fef-155">Client disconnects are detected.</span></span> <span data-ttu-id="75fef-156">El token de cancelación de [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="75fef-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="75fef-157">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="75fef-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="75fef-158">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="75fef-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="75fef-159">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="75fef-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="75fef-160">No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).</span><span class="sxs-lookup"><span data-stu-id="75fef-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
