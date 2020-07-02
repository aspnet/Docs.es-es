---
title: Novedades de ASP.NET Core 2.0
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.0
ms.openlocfilehash: 97d8606ba6bef0bb8bf23d1553c7cb5681fbdbd8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404371"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="71273-103">Novedades de ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="71273-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="71273-104">En este artículo se resaltan los cambios más importantes de ASP.NET Core 2.0, con vínculos a la documentación pertinente.</span><span class="sxs-lookup"><span data-stu-id="71273-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a>Razor<span data-ttu-id="71273-105"> Pages</span><span class="sxs-lookup"><span data-stu-id="71273-105"> Pages</span></span>

Razor<span data-ttu-id="71273-106"> Pages es una nueva característica de ASP.NET Core MVC que facilita la codificación de escenarios centrados en páginas y hace que sea más productiva.</span><span class="sxs-lookup"><span data-stu-id="71273-106"> Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="71273-107">Para más información, vea la introducción y el tutorial:</span><span class="sxs-lookup"><span data-stu-id="71273-107">For more information, see the introduction and tutorial:</span></span>

* <span data-ttu-id="71273-108">[Introducción a Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="71273-108">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="71273-109">[Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="71273-109">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="71273-110">Metapaquete de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71273-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="71273-111">Hay un nuevo metapaquete de ASP.NET Core que incluye todos los paquetes creados y que son compatibles con los equipos de ASP.NET Core y Entity Framework Core, junto con sus dependencias internas y de terceros.</span><span class="sxs-lookup"><span data-stu-id="71273-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="71273-112">Ya no tiene que elegir características concretas de ASP.NET Core por paquete.</span><span class="sxs-lookup"><span data-stu-id="71273-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="71273-113">Todas las características se incluyen en el paquete [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All).</span><span class="sxs-lookup"><span data-stu-id="71273-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="71273-114">Las plantillas predeterminadas usan este paquete.</span><span class="sxs-lookup"><span data-stu-id="71273-114">The default templates use this package.</span></span>

<span data-ttu-id="71273-115">Para más información, vea [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage) (Metapaquete Microsoft.AspNetCore.All para ASP.NET Core 2.0).</span><span class="sxs-lookup"><span data-stu-id="71273-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="71273-116">Almacén en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="71273-116">Runtime Store</span></span>

<span data-ttu-id="71273-117">Las aplicaciones que usan el metapaquete `Microsoft.AspNetCore.All` pueden aprovechar automáticamente el nuevo almacén en tiempo de ejecución de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="71273-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="71273-118">El almacén contiene todos los recursos en tiempo de ejecución necesarios para ejecutar aplicaciones de ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="71273-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="71273-119">Al usar el metapaquete `Microsoft.AspNetCore.All`, no se implementa ningún recurso de los paquetes NuGet de ASP.NET Core referenciados con la aplicación, porque ya residen en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="71273-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="71273-120">Los recursos del almacén en tiempo de ejecución también se precompilan para mejorar el tiempo de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="71273-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="71273-121">Para más información, vea [Runtime store](/dotnet/core/deploying/runtime-store) (Almacén en tiempo de ejecución).</span><span class="sxs-lookup"><span data-stu-id="71273-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="71273-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="71273-122">.NET Standard 2.0</span></span>

<span data-ttu-id="71273-123">Los paquetes de ASP.NET Core 2.0 tienen como destino .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="71273-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="71273-124">Se puede hacer referencia a los paquetes mediante otras bibliotecas de .NET Standard 2.0 y se pueden ejecutar en implementaciones compatibles con .NET Standard 2.0 de. NET, como .NET Core 2.0 y .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="71273-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="71273-125">El metapaquete `Microsoft.AspNetCore.All` tiene como destino únicamente .NET Core 2.0, ya que está pensado para usarse con el almacén en tiempo de ejecución de .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="71273-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="71273-126">Actualización de la configuración</span><span class="sxs-lookup"><span data-stu-id="71273-126">Configuration update</span></span>

<span data-ttu-id="71273-127">En ASP.NET Core 2.0 se agrega de forma predeterminada una instancia `IConfiguration` al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="71273-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="71273-128">La instancia `IConfiguration` del contenedor de servicios facilita que las aplicaciones recuperen los valores de configuración del contenedor.</span><span class="sxs-lookup"><span data-stu-id="71273-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="71273-129">Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="71273-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="71273-130">Actualización del registro</span><span class="sxs-lookup"><span data-stu-id="71273-130">Logging update</span></span>

<span data-ttu-id="71273-131">En ASP.NET Core 2.0, el registro se incorpora de forma predeterminada en el sistema de inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="71273-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="71273-132">Debe agregar proveedores y configurar el filtrado en el archivo *Program.cs*, y no en el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="71273-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="71273-133">El `ILoggerFactory` predeterminado admite el filtrado de una forma que le permite usar un enfoque flexible para el filtrado de varios proveedores y el filtrado de proveedor específico.</span><span class="sxs-lookup"><span data-stu-id="71273-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="71273-134">Para más información, vea [Introduction to Logging](xref:fundamentals/logging/index) (Introducción al registro).</span><span class="sxs-lookup"><span data-stu-id="71273-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="71273-135">Actualización de la autenticación</span><span class="sxs-lookup"><span data-stu-id="71273-135">Authentication update</span></span>

<span data-ttu-id="71273-136">Hay un nuevo modelo de autenticación que facilita la configuración de la autenticación de una aplicación mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="71273-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="71273-137">Hay plantillas nuevas disponibles para configurar la autenticación de aplicaciones web y API web con [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="71273-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="71273-138">Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="71273-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="71273-139">Actualización de Identity</span><span class="sxs-lookup"><span data-stu-id="71273-139">Identity update</span></span>

<span data-ttu-id="71273-140">Se ha facilitado la compilación de API web seguras mediante Identity de ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="71273-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="71273-141">Puede adquirir tokens de acceso para obtener acceso a las API web mediante la [Biblioteca de autenticación de Microsoft (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="71273-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="71273-142">Para más información sobre los cambios de autenticación en la versión 2.0, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="71273-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="71273-143">Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71273-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="71273-144">Habilitar la generación de códigos QR para las aplicaciones de autenticación en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71273-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* <span data-ttu-id="71273-145">[Migración de autenticación e Identity a ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)</span><span class="sxs-lookup"><span data-stu-id="71273-145">[Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)</span></span>

## <a name="spa-templates"></a><span data-ttu-id="71273-146">Plantillas de SPA</span><span class="sxs-lookup"><span data-stu-id="71273-146">SPA templates</span></span>

<span data-ttu-id="71273-147">Hay disponibles plantillas de proyectos de Single-Page Application (SPA) para Angular, Aurelia, Knockout.js, React.js y React.js con Redux.</span><span class="sxs-lookup"><span data-stu-id="71273-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="71273-148">La plantilla de Angular se ha actualizado a Angular 4.</span><span class="sxs-lookup"><span data-stu-id="71273-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="71273-149">Las plantillas de Angular y de React están disponibles de forma predeterminada. Para obtener información sobre cómo obtener las otras plantillas, vea [Creating a new SPA project](xref:client-side/spa-services#create-a-new-project) (Crear un proyecto de SPA).</span><span class="sxs-lookup"><span data-stu-id="71273-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="71273-150">Para obtener información acerca de cómo crear una SPA en ASP.NET Core, vea <xref:client-side/spa-services>.</span><span class="sxs-lookup"><span data-stu-id="71273-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="71273-151">Mejoras en Kestrel</span><span class="sxs-lookup"><span data-stu-id="71273-151">Kestrel improvements</span></span>

<span data-ttu-id="71273-152">El servidor web de Kestrel tiene nuevas características que lo hacen más adecuado como servidor con conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="71273-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="71273-153">Se ha agregado una serie de opciones de configuración de restricción del servidor en la nueva propiedad `Limits` de la clase `KestrelServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="71273-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="71273-154">Agregue límites para:</span><span class="sxs-lookup"><span data-stu-id="71273-154">Add limits for the following:</span></span>

* <span data-ttu-id="71273-155">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="71273-155">Maximum client connections</span></span>
* <span data-ttu-id="71273-156">El tamaño máximo del cuerpo de solicitud</span><span class="sxs-lookup"><span data-stu-id="71273-156">Maximum request body size</span></span>
* <span data-ttu-id="71273-157">La velocidad mínima de los datos del cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="71273-157">Minimum request body data rate</span></span>

<span data-ttu-id="71273-158">Para más información, vea [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel) (Implementación del servidor web de Kestrel en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="71273-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="71273-159">WebListener pasa a denominarse HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="71273-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="71273-160">Los paquetes `Microsoft.AspNetCore.Server.WebListener` y `Microsoft.Net.Http.Server` se han combinado en un nuevo paquete, `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="71273-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="71273-161">Los espacios de nombres se han actualizado para que coincidan.</span><span class="sxs-lookup"><span data-stu-id="71273-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="71273-162">Para más información, vea [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys) (Implementaciones del servidor web de HTTP.sys en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="71273-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="71273-163">Compatibilidad mejorada de los encabezados HTTP</span><span class="sxs-lookup"><span data-stu-id="71273-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="71273-164">Al usar MVC para transmitir un `FileStreamResult` o un `FileContentResult`, ahora tiene la opción de establecer una `ETag` o una fecha `LastModified` en el contenido que se transmite.</span><span class="sxs-lookup"><span data-stu-id="71273-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="71273-165">Puede establecer estos valores en el contenido devuelto con un código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="71273-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="71273-166">El archivo devuelto a los visitantes incluye los encabezados HTTP adecuados para los valores `ETag` y `LastModified`.</span><span class="sxs-lookup"><span data-stu-id="71273-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="71273-167">Si un visitante de la aplicación solicita el contenido con un encabezado de solicitud de intervalo, ASP.NET Core reconoce la solicitud y controla ese encabezado.</span><span class="sxs-lookup"><span data-stu-id="71273-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="71273-168">Si el contenido solicitado se puede entregar de forma parcial, ASP.NET Core lo omite debidamente y solo devuelve el conjunto de bytes solicitado.</span><span class="sxs-lookup"><span data-stu-id="71273-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="71273-169">No es necesario que escriba ningún controlador especial en los métodos para adaptar o controlar esta característica, ya que se controla automáticamente.</span><span class="sxs-lookup"><span data-stu-id="71273-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="71273-170">Inicio del hospedaje y Application Insights</span><span class="sxs-lookup"><span data-stu-id="71273-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="71273-171">Ahora, los entornos de hospedaje pueden insertar dependencias de paquetes adicionales y ejecutar código durante el inicio de la aplicación sin que la aplicación tenga que tomar una dependencia explícitamente o llamar a ningún método.</span><span class="sxs-lookup"><span data-stu-id="71273-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="71273-172">Esta característica se puede usar para habilitar ciertos entornos y activar características únicas de ese entorno sin que la aplicación tenga que saberlo de antemano.</span><span class="sxs-lookup"><span data-stu-id="71273-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="71273-173">En ASP.NET Core 2.0, esta característica se usa para habilitar automáticamente los diagnósticos de Application Insights al efectuar una depuración en Visual Studio y (tras la participación) al ejecutarse en Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="71273-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="71273-174">Como resultado, las plantillas del proyecto ya no agregan de forma predeterminada el código ni los paquetes de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="71273-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="71273-175">Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="71273-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="71273-176">Uso automático de tokens antifalsificación</span><span class="sxs-lookup"><span data-stu-id="71273-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="71273-177">ASP.NET Core siempre ha ayudado a codificar en HTML el contenido de forma predeterminada, pero con la nueva versión estamos dando un paso más para impedir ataques de falsificación de solicitud entre sitios (CSRF).</span><span class="sxs-lookup"><span data-stu-id="71273-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="71273-178">A partir de ahora, ASP.NET Core emitirá tokens antifalsificación de forma predeterminada y los validará en las páginas y acciones POST de formulario sin tener que aplicar ninguna configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="71273-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="71273-179">Para más información, vea [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks](xref:security/anti-request-forgery) (Evitar los ataques de falsificación de solicitud entre sitios [XSRF/CSRF]).</span><span class="sxs-lookup"><span data-stu-id="71273-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="71273-180">Precompilación automática</span><span class="sxs-lookup"><span data-stu-id="71273-180">Automatic precompilation</span></span>

<span data-ttu-id="71273-181">La precompilación de vistas de Razor se habilita de forma predeterminada durante la publicación, lo que reduce el tamaño de salida de la publicación y el tiempo de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="71273-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="71273-182">Para obtener más información, vea [Precompilación y compilación de vistas de Razor en ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="71273-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="71273-183">Compatibilidad de Razor con C# 7.1</span><span class="sxs-lookup"><span data-stu-id="71273-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="71273-184">El motor de vistas de Razor se ha actualizado para poder funcionar con el nuevo compilador Roslyn.</span><span class="sxs-lookup"><span data-stu-id="71273-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="71273-185">Incluye compatibilidad con características de C# 7.1, como las expresiones predeterminadas, los nombres de tupla inferidos y la coincidencia de patrones con genéricos.</span><span class="sxs-lookup"><span data-stu-id="71273-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="71273-186">Para usar C# 7.1 en el proyecto, agregue la siguiente propiedad al archivo del proyecto y, luego, vuelva a cargar la solución:</span><span class="sxs-lookup"><span data-stu-id="71273-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="71273-187">Para información sobre el estado de las características de C# 7.1, vea [el repositorio de GitHub para Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="71273-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="71273-188">Otras actualizaciones de documentación para la versión 2.0</span><span class="sxs-lookup"><span data-stu-id="71273-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="71273-189">Perfiles de publicación de Visual Studio para el desarrollo de aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71273-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="71273-190">Administración de claves</span><span class="sxs-lookup"><span data-stu-id="71273-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="71273-191">Configurar la autenticación de Facebook</span><span class="sxs-lookup"><span data-stu-id="71273-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="71273-192">Configurar la autenticación de Twitter</span><span class="sxs-lookup"><span data-stu-id="71273-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="71273-193">Configurar la autenticación de Google</span><span class="sxs-lookup"><span data-stu-id="71273-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="71273-194">Configurar la autenticación de la cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="71273-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="71273-195">Guía de migración</span><span class="sxs-lookup"><span data-stu-id="71273-195">Migration guidance</span></span>

<span data-ttu-id="71273-196">Para obtener instrucciones sobre cómo migrar aplicaciones de ASP.NET Core 1.x a ASP.NET Core 2.0, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="71273-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="71273-197">Migración de ASP.NET Core 1.x a ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="71273-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* <span data-ttu-id="71273-198">[Migración de autenticación e Identity a ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)</span><span class="sxs-lookup"><span data-stu-id="71273-198">[Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)</span></span>

## <a name="additional-information"></a><span data-ttu-id="71273-199">Información adicional</span><span class="sxs-lookup"><span data-stu-id="71273-199">Additional Information</span></span>

<span data-ttu-id="71273-200">Para ver la lista completa de cambios, consulte las [notas de la versión de ASP.NET Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="71273-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="71273-201">Para estar en contacto con el progreso y los planes del equipo de desarrollo de ASP.NET Core, sintonice [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="71273-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
