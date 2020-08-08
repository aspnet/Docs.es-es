---
title: Aplicación de HTTPS en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo requerir HTTPS/TLS en una aplicación Web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: 5dcdf50ff9f750e4966ed3bdf24a71b9f433240a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019006"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="45dc3-103">Aplicación de HTTPS en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45dc3-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="45dc3-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="45dc3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="45dc3-105">En este documento se muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="45dc3-105">This document shows how to:</span></span>

* <span data-ttu-id="45dc3-106">Requiere HTTPS para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="45dc3-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="45dc3-107">Redirija todas las solicitudes HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="45dc3-108">Ninguna API puede impedir que un cliente envíe datos confidenciales en la primera solicitud.</span><span class="sxs-lookup"><span data-stu-id="45dc3-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="45dc3-109">Proyectos de API</span><span class="sxs-lookup"><span data-stu-id="45dc3-109">API projects</span></span>
>
> <span data-ttu-id="45dc3-110">**No** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) en las API Web que reciben información confidencial.</span><span class="sxs-lookup"><span data-stu-id="45dc3-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="45dc3-111">`RequireHttpsAttribute`utiliza códigos de Estado HTTP para redirigir exploradores de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="45dc3-112">Es posible que los clientes de API no conozcan o cumplan las redirecciones de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="45dc3-113">Estos clientes pueden enviar información a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="45dc3-114">Las API Web deben:</span><span class="sxs-lookup"><span data-stu-id="45dc3-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="45dc3-115">No escuche en HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="45dc3-116">Cierre la conexión con el código de estado 400 (solicitud incorrecta) y no atienda la solicitud.</span><span class="sxs-lookup"><span data-stu-id="45dc3-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="45dc3-117">Proyectos de HSTS y API</span><span class="sxs-lookup"><span data-stu-id="45dc3-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="45dc3-118">Los proyectos de API predeterminados no incluyen [HSTS](#hsts) porque HSTS suele ser una instrucción solo del explorador.</span><span class="sxs-lookup"><span data-stu-id="45dc3-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="45dc3-119">Otros llamadores, como las aplicaciones de escritorio o de teléfono, **no** obedecen a la instrucción.</span><span class="sxs-lookup"><span data-stu-id="45dc3-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="45dc3-120">Incluso dentro de los exploradores, una única llamada autenticada a una API sobre HTTP tiene riesgos en las redes no seguras.</span><span class="sxs-lookup"><span data-stu-id="45dc3-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="45dc3-121">El enfoque seguro consiste en configurar proyectos de API para que solo escuchen y respondan a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="45dc3-122">Proyectos de API</span><span class="sxs-lookup"><span data-stu-id="45dc3-122">API projects</span></span>
>
> <span data-ttu-id="45dc3-123">**No** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) en las API Web que reciben información confidencial.</span><span class="sxs-lookup"><span data-stu-id="45dc3-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="45dc3-124">`RequireHttpsAttribute`utiliza códigos de Estado HTTP para redirigir exploradores de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="45dc3-125">Es posible que los clientes de API no conozcan o cumplan las redirecciones de HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="45dc3-126">Estos clientes pueden enviar información a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="45dc3-127">Las API Web deben:</span><span class="sxs-lookup"><span data-stu-id="45dc3-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="45dc3-128">No escuche en HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="45dc3-129">Cierre la conexión con el código de estado 400 (solicitud incorrecta) y no atienda la solicitud.</span><span class="sxs-lookup"><span data-stu-id="45dc3-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="45dc3-130">Requerir HTTPS</span><span class="sxs-lookup"><span data-stu-id="45dc3-130">Require HTTPS</span></span>

<span data-ttu-id="45dc3-131">Se recomienda que las aplicaciones Web de producción ASP.NET Core usen:</span><span class="sxs-lookup"><span data-stu-id="45dc3-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="45dc3-132">Middleware de redirección de HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) para redirigir las solicitudes HTTP a https.</span><span class="sxs-lookup"><span data-stu-id="45dc3-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="45dc3-133">Middleware de HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar encabezados del Protocolo de seguridad de transporte estricto (HSTS) http a los clientes.</span><span class="sxs-lookup"><span data-stu-id="45dc3-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="45dc3-134">Las aplicaciones implementadas en una configuración de proxy inverso permiten que el proxy controle la seguridad de conexión (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="45dc3-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="45dc3-135">Si el proxy también controla el redireccionamiento de HTTPS, no es necesario usar el middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="45dc3-136">Si el servidor proxy también controla la escritura de encabezados HSTS (por ejemplo, la [compatibilidad con HSTS nativa en IIS 10,0 (1709) o posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), la aplicación no requiere el middleware HSTS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="45dc3-137">Para obtener más información, consulte no [participar en https/HSTS en la creación de proyectos](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="45dc3-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="45dc3-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="45dc3-138">UseHttpsRedirection</span></span>

<span data-ttu-id="45dc3-139">El código siguiente llama a `UseHttpsRedirection` en la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="45dc3-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="45dc3-140">Código resaltado anterior:</span><span class="sxs-lookup"><span data-stu-id="45dc3-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="45dc3-141">Usa el valor predeterminado [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="45dc3-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="45dc3-142">Usa el valor predeterminado de [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (NULL) a menos que se reemplace por la `ASPNETCORE_HTTPS_PORT` variable de entorno o [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="45dc3-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="45dc3-143">Se recomienda el uso de redirecciones temporales en lugar de redireccionamientos permanentes.</span><span class="sxs-lookup"><span data-stu-id="45dc3-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="45dc3-144">El almacenamiento en caché de vínculos puede producir un comportamiento inestable en entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="45dc3-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="45dc3-145">Si prefiere enviar un código de estado de redirección permanente cuando la aplicación se encuentra en un entorno que no es de desarrollo, consulte la sección [configuración de redirecciones permanentes en producción](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="45dc3-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="45dc3-146">Se recomienda usar [HSTS](#http-strict-transport-security-protocol-hsts) para indicar a los clientes que solo se deben enviar solicitudes de recursos seguros a la aplicación (solo en producción).</span><span class="sxs-lookup"><span data-stu-id="45dc3-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="45dc3-147">Configuración de puerto</span><span class="sxs-lookup"><span data-stu-id="45dc3-147">Port configuration</span></span>

<span data-ttu-id="45dc3-148">Un puerto debe estar disponible para que el middleware redirija una solicitud no segura a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="45dc3-149">Si no hay ningún puerto disponible:</span><span class="sxs-lookup"><span data-stu-id="45dc3-149">If no port is available:</span></span>

* <span data-ttu-id="45dc3-150">No se produce la redirección a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="45dc3-151">El middleware registra la advertencia "no se pudo determinar el puerto https para la redirección".</span><span class="sxs-lookup"><span data-stu-id="45dc3-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="45dc3-152">Especifique el Puerto HTTPS mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="45dc3-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="45dc3-153">Establezca [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="45dc3-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="45dc3-154">Establezca la `https_port` [configuración de host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="45dc3-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="45dc3-155">En configuración de host.</span><span class="sxs-lookup"><span data-stu-id="45dc3-155">In host configuration.</span></span>
  * <span data-ttu-id="45dc3-156">Estableciendo la `ASPNETCORE_HTTPS_PORT` variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="45dc3-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="45dc3-157">Agregando una entrada de nivel superior en *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="45dc3-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="45dc3-158">Indique un puerto con el esquema seguro mediante la [variable de entorno ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="45dc3-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="45dc3-159">La variable de entorno configura el servidor.</span><span class="sxs-lookup"><span data-stu-id="45dc3-159">The environment variable configures the server.</span></span> <span data-ttu-id="45dc3-160">El middleware detecta indirectamente el Puerto HTTPS a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="45dc3-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="45dc3-161">Este enfoque no funciona en las implementaciones de proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="45dc3-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="45dc3-162">Establezca la `https_port` [configuración de host](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="45dc3-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="45dc3-163">En configuración de host.</span><span class="sxs-lookup"><span data-stu-id="45dc3-163">In host configuration.</span></span>
  * <span data-ttu-id="45dc3-164">Estableciendo la `ASPNETCORE_HTTPS_PORT` variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="45dc3-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="45dc3-165">Agregando una entrada de nivel superior en *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="45dc3-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="45dc3-166">Indique un puerto con el esquema seguro mediante la [variable de entorno ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="45dc3-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="45dc3-167">La variable de entorno configura el servidor.</span><span class="sxs-lookup"><span data-stu-id="45dc3-167">The environment variable configures the server.</span></span> <span data-ttu-id="45dc3-168">El middleware detecta indirectamente el Puerto HTTPS a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="45dc3-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="45dc3-169">Este enfoque no funciona en las implementaciones de proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="45dc3-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="45dc3-170">En desarrollo, establezca una dirección URL HTTPS en *launchsettings.jsen*.</span><span class="sxs-lookup"><span data-stu-id="45dc3-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="45dc3-171">Habilite HTTPS cuando se use IIS Express.</span><span class="sxs-lookup"><span data-stu-id="45dc3-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="45dc3-172">Configure un punto de conexión de dirección URL HTTPS para una implementación perimetral de acceso público del servidor [Kestrel](xref:fundamentals/servers/kestrel) o [HTTP.sys](xref:fundamentals/servers/httpsys) Server.</span><span class="sxs-lookup"><span data-stu-id="45dc3-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="45dc3-173">La aplicación solo usa **un puerto https** .</span><span class="sxs-lookup"><span data-stu-id="45dc3-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="45dc3-174">El middleware detecta el puerto a través de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="45dc3-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="45dc3-175">Cuando una aplicación se ejecuta en una configuración de proxy inverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="45dc3-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="45dc3-176">Establezca el puerto con uno de los otros enfoques descritos en esta sección.</span><span class="sxs-lookup"><span data-stu-id="45dc3-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="45dc3-177">Implementaciones de Edge</span><span class="sxs-lookup"><span data-stu-id="45dc3-177">Edge deployments</span></span> 

<span data-ttu-id="45dc3-178">Cuando se usa Kestrel o HTTP.sys como servidor perimetral de acceso público, se debe configurar Kestrel o HTTP.sys para que escuche en ambos:</span><span class="sxs-lookup"><span data-stu-id="45dc3-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="45dc3-179">El puerto seguro al que se redirige el cliente (normalmente, 443 en producción y 5001 en desarrollo).</span><span class="sxs-lookup"><span data-stu-id="45dc3-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="45dc3-180">El puerto no seguro (normalmente, 80 en producción y 5000 en desarrollo).</span><span class="sxs-lookup"><span data-stu-id="45dc3-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="45dc3-181">El cliente debe tener acceso al puerto inseguro para que la aplicación reciba una solicitud no segura y redirija el cliente al puerto seguro.</span><span class="sxs-lookup"><span data-stu-id="45dc3-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="45dc3-182">Para obtener más información, vea [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) o <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="45dc3-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="45dc3-183">Escenarios de implementación</span><span class="sxs-lookup"><span data-stu-id="45dc3-183">Deployment scenarios</span></span>

<span data-ttu-id="45dc3-184">Cualquier firewall entre el cliente y el servidor también debe tener puertos de comunicación abiertos para el tráfico.</span><span class="sxs-lookup"><span data-stu-id="45dc3-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="45dc3-185">Si las solicitudes se reenvían en una configuración de proxy inverso, use el [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) antes de llamar al middleware de redireccionamiento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="45dc3-186">El middleware de encabezados reenviados actualiza el `Request.Scheme` , mediante el `X-Forwarded-Proto` encabezado.</span><span class="sxs-lookup"><span data-stu-id="45dc3-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="45dc3-187">El middleware permite que los URI de redirección y otras directivas de seguridad funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="45dc3-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="45dc3-188">Cuando no se usa middleware de encabezados reenviados, es posible que la aplicación de back-end no reciba el esquema correcto y acabe en un bucle de redirección.</span><span class="sxs-lookup"><span data-stu-id="45dc3-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="45dc3-189">Un mensaje de error común del usuario final es que se han producido demasiadas redirecciones.</span><span class="sxs-lookup"><span data-stu-id="45dc3-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="45dc3-190">Al implementar en Azure App Service, siga las instrucciones de [Tutorial: enlazar un certificado SSL personalizado existente a Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="45dc3-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="45dc3-191">Opciones</span><span class="sxs-lookup"><span data-stu-id="45dc3-191">Options</span></span>

<span data-ttu-id="45dc3-192">El siguiente código resaltado llama a [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar las opciones de middleware:</span><span class="sxs-lookup"><span data-stu-id="45dc3-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="45dc3-193">Llamar a `AddHttpsRedirection` solo es necesario para cambiar los valores de `HttpsPort` o `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="45dc3-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="45dc3-194">Código resaltado anterior:</span><span class="sxs-lookup"><span data-stu-id="45dc3-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="45dc3-195">Establece [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) en <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> , que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="45dc3-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="45dc3-196">Utilice los campos de la <xref:Microsoft.AspNetCore.Http.StatusCodes> clase para las asignaciones a `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="45dc3-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="45dc3-197">Establece el Puerto HTTPS en 5001.</span><span class="sxs-lookup"><span data-stu-id="45dc3-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="45dc3-198">Configuración de redirecciones permanentes en producción</span><span class="sxs-lookup"><span data-stu-id="45dc3-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="45dc3-199">De forma predeterminada, el middleware envía un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) con todos los redireccionamientos.</span><span class="sxs-lookup"><span data-stu-id="45dc3-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="45dc3-200">Si prefiere enviar un código de estado de redirección permanente cuando la aplicación se encuentra en un entorno que no es de desarrollo, ajuste la configuración de las opciones de middleware en una comprobación condicional para un entorno que no sea de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="45dc3-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="45dc3-201">Al configurar servicios en *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="45dc3-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="45dc3-202">Al configurar servicios en *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="45dc3-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="45dc3-203">Enfoque alternativo de middleware de redirección de HTTPS</span><span class="sxs-lookup"><span data-stu-id="45dc3-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="45dc3-204">Una alternativa al uso del middleware de redirección de HTTPS ( `UseHttpsRedirection` ) es usar el middleware de reescritura de direcciones URL ( `AddRedirectToHttps` ).</span><span class="sxs-lookup"><span data-stu-id="45dc3-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="45dc3-205">`AddRedirectToHttps`también puede establecer el código de estado y el puerto cuando se ejecuta la redirección.</span><span class="sxs-lookup"><span data-stu-id="45dc3-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="45dc3-206">Para obtener más información, vea [middleware de reescritura de direcciones URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="45dc3-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="45dc3-207">Al redirigir a HTTPS sin necesidad de reglas de redirección adicionales, se recomienda usar el middleware de redireccionamiento `UseHttpsRedirection` de HTTPS () que se describe en este tema.</span><span class="sxs-lookup"><span data-stu-id="45dc3-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="45dc3-208">Protocolo de seguridad de transporte estricto HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="45dc3-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="45dc3-209">Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), la [seguridad de transporte estricta http (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) es una mejora de seguridad opcional que se especifica mediante una aplicación Web mediante el uso de un encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="45dc3-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="45dc3-210">Cuando un [explorador que admite HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) recibe este encabezado:</span><span class="sxs-lookup"><span data-stu-id="45dc3-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="45dc3-211">El explorador almacena la configuración del dominio que evita el envío de cualquier comunicación a través de HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="45dc3-212">El explorador fuerza toda la comunicación a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="45dc3-213">El explorador impide que el usuario Use certificados no confiables o no válidos.</span><span class="sxs-lookup"><span data-stu-id="45dc3-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="45dc3-214">El explorador deshabilita los mensajes que permiten a un usuario confiar temporalmente en este tipo de certificado.</span><span class="sxs-lookup"><span data-stu-id="45dc3-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="45dc3-215">Dado que el cliente exige HSTS, tiene algunas limitaciones:</span><span class="sxs-lookup"><span data-stu-id="45dc3-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="45dc3-216">El cliente debe admitir HSTS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-216">The client must support HSTS.</span></span>
* <span data-ttu-id="45dc3-217">HSTS requiere al menos una solicitud HTTPS correcta para establecer la Directiva HSTS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="45dc3-218">La aplicación debe comprobar cada solicitud HTTP y redirigir o rechazar la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="45dc3-219">ASP.NET Core 2,1 y posteriores implementa HSTS con el `UseHsts` método de extensión.</span><span class="sxs-lookup"><span data-stu-id="45dc3-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="45dc3-220">El código siguiente llama a `UseHsts` cuando la aplicación no está en [modo de desarrollo](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="45dc3-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="45dc3-221">`UseHsts`no se recomienda en el desarrollo porque los exploradores pueden almacenar en memoria caché los valores de HSTS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="45dc3-222">De forma predeterminada, `UseHsts` excluye la dirección de bucle invertido local.</span><span class="sxs-lookup"><span data-stu-id="45dc3-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="45dc3-223">En el caso de los entornos de producción que implementan HTTPS por primera vez, establezca [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) inicial en un valor pequeño utilizando uno de los <xref:System.TimeSpan> métodos.</span><span class="sxs-lookup"><span data-stu-id="45dc3-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="45dc3-224">Establezca el valor de horas en no más de un día único en caso de que necesite revertir la infraestructura HTTPS a HTTP.</span><span class="sxs-lookup"><span data-stu-id="45dc3-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="45dc3-225">Después de estar seguro de la sostenibilidad de la configuración de HTTPS, aumente el `max-age` valor de HSTS; un valor de uso común es de un año.</span><span class="sxs-lookup"><span data-stu-id="45dc3-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="45dc3-226">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="45dc3-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="45dc3-227">Establece el parámetro preload del `Strict-Transport-Security` encabezado.</span><span class="sxs-lookup"><span data-stu-id="45dc3-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="45dc3-228">La precarga no forma parte de la [especificación RFC HSTS](https://tools.ietf.org/html/rfc6797), pero es compatible con los exploradores Web para precargar sitios de HSTS en la instalación nueva.</span><span class="sxs-lookup"><span data-stu-id="45dc3-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="45dc3-229">Para obtener más información, vea [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="45dc3-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="45dc3-230">Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que aplica la Directiva HSTS para hospedar subdominios.</span><span class="sxs-lookup"><span data-stu-id="45dc3-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="45dc3-231">Establece explícitamente el `max-age` parámetro del `Strict-Transport-Security` encabezado en 60 días.</span><span class="sxs-lookup"><span data-stu-id="45dc3-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="45dc3-232">Si no se establece, el valor predeterminado es 30 días.</span><span class="sxs-lookup"><span data-stu-id="45dc3-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="45dc3-233">Para obtener más información, vea la [Directiva Max-Age](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="45dc3-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="45dc3-234">Agrega `example.com` a la lista de hosts que se van a excluir.</span><span class="sxs-lookup"><span data-stu-id="45dc3-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="45dc3-235">`UseHsts`excluye los siguientes hosts de bucle invertido:</span><span class="sxs-lookup"><span data-stu-id="45dc3-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="45dc3-236">`localhost`: La dirección de bucle invertido IPv4.</span><span class="sxs-lookup"><span data-stu-id="45dc3-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="45dc3-237">`127.0.0.1`: La dirección de bucle invertido IPv4.</span><span class="sxs-lookup"><span data-stu-id="45dc3-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="45dc3-238">`[::1]`: Dirección de bucle invertido IPv6.</span><span class="sxs-lookup"><span data-stu-id="45dc3-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="45dc3-239">No participar en HTTPS/HSTS en la creación de proyectos</span><span class="sxs-lookup"><span data-stu-id="45dc3-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="45dc3-240">En algunos escenarios de servicio de back-end en los que la seguridad de la conexión se controla en el perímetro de acceso público de la red, no es necesario configurar la seguridad de conexión en cada nodo.</span><span class="sxs-lookup"><span data-stu-id="45dc3-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="45dc3-241">Las aplicaciones web que se generan a partir de las plantillas en Visual Studio o desde el comando [dotnet New](/dotnet/core/tools/dotnet-new) habilitan la [redirección de https](#require-https) y [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="45dc3-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="45dc3-242">En el caso de las implementaciones que no requieren estos escenarios, puede optar por HTTPS/HSTS cuando la aplicación se crea a partir de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="45dc3-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="45dc3-243">Para no participar en HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="45dc3-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45dc3-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45dc3-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="45dc3-245">Desactive la casilla **configurar para https** .</span><span class="sxs-lookup"><span data-stu-id="45dc3-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Cuadro de diálogo Nueva ASP.NET Core aplicación web que muestra la casilla de verificación configurar para HTTPS no seleccionada.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Cuadro de diálogo Nueva ASP.NET Core aplicación web que muestra la casilla de verificación configurar para HTTPS no seleccionada.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="45dc3-248">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="45dc3-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="45dc3-249">Use la opción `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="45dc3-249">Use the `--no-https` option.</span></span> <span data-ttu-id="45dc3-250">Por ejemplo</span><span class="sxs-lookup"><span data-stu-id="45dc3-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="45dc3-251">Confíe en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS</span><span class="sxs-lookup"><span data-stu-id="45dc3-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="45dc3-252">El SDK de .NET Core incluye un certificado de desarrollo de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="45dc3-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="45dc3-253">El certificado se instala como parte de la experiencia de primera ejecución.</span><span class="sxs-lookup"><span data-stu-id="45dc3-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="45dc3-254">Por ejemplo, `dotnet --info` genera una variación de la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="45dc3-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="45dc3-255">Al instalar el SDK de .NET Core se instala el certificado de desarrollo HTTPS de ASP.NET Core en el almacén de certificados local del usuario.</span><span class="sxs-lookup"><span data-stu-id="45dc3-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="45dc3-256">El certificado se ha instalado, pero no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="45dc3-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="45dc3-257">Para confiar en el certificado, realice el paso de una vez para ejecutar la `dev-certs` herramienta dotnet:</span><span class="sxs-lookup"><span data-stu-id="45dc3-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="45dc3-258">El siguiente comando proporciona ayuda sobre la herramienta `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="45dc3-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="45dc3-259">Cómo configurar un certificado de desarrollador para Docker</span><span class="sxs-lookup"><span data-stu-id="45dc3-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="45dc3-260">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="45dc3-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="45dc3-261">Confiar en el certificado HTTPS del subsistema de Windows para Linux</span><span class="sxs-lookup"><span data-stu-id="45dc3-261">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="45dc3-262">El subsistema de Windows para Linux (WSL) genera un certificado autofirmado HTTPS. Para configurar el almacén de certificados de Windows para que confíe en el certificado WSL:</span><span class="sxs-lookup"><span data-stu-id="45dc3-262">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="45dc3-263">Ejecute el siguiente comando para exportar el certificado generado por WSL:</span><span class="sxs-lookup"><span data-stu-id="45dc3-263">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="45dc3-264">En una ventana de WSL, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="45dc3-264">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="45dc3-265">El comando anterior establece las variables de entorno, por lo que Linux usa el certificado de confianza de Windows.</span><span class="sxs-lookup"><span data-stu-id="45dc3-265">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="45dc3-266">Solucionar problemas de certificados</span><span class="sxs-lookup"><span data-stu-id="45dc3-266">Troubleshoot certificate problems</span></span>

<span data-ttu-id="45dc3-267">En esta sección se proporciona ayuda cuando se ha [instalado y se confía](#trust)en el certificado de desarrollo de ASP.net Core https, pero sigue teniendo en cuenta las advertencias del explorador de que el certificado no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="45dc3-267">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="45dc3-268">[Kestrel](xref:fundamentals/servers/kestrel)usa el certificado de desarrollo de https ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="45dc3-268">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="45dc3-269">Todas las plataformas: el certificado no es de confianza</span><span class="sxs-lookup"><span data-stu-id="45dc3-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="45dc3-270">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="45dc3-270">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="45dc3-271">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="45dc3-271">Close any browser instances open.</span></span> <span data-ttu-id="45dc3-272">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45dc3-272">Open a new browser window to app.</span></span> <span data-ttu-id="45dc3-273">Los exploradores almacenan en caché la confianza de certificados.</span><span class="sxs-lookup"><span data-stu-id="45dc3-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="45dc3-274">Los comandos anteriores solucionan la mayoría de los problemas de confianza del explorador.</span><span class="sxs-lookup"><span data-stu-id="45dc3-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="45dc3-275">Si el explorador sigue sin confiar en el certificado, siga las sugerencias específicas de la plataforma que se indican a continuación.</span><span class="sxs-lookup"><span data-stu-id="45dc3-275">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="45dc3-276">Docker: certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="45dc3-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="45dc3-277">Elimine la carpeta *C:\Users \{ User} \AppData\Roaming\ASP.NET\Https*</span><span class="sxs-lookup"><span data-stu-id="45dc3-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="45dc3-278">Limpie la solución.</span><span class="sxs-lookup"><span data-stu-id="45dc3-278">Clean the solution.</span></span> <span data-ttu-id="45dc3-279">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="45dc3-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="45dc3-280">Reinicie la herramienta de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="45dc3-280">Restart the development tool.</span></span> <span data-ttu-id="45dc3-281">Por ejemplo, Visual Studio, Visual Studio Code o Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="45dc3-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="45dc3-282">Windows-certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="45dc3-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="45dc3-283">Compruebe los certificados en el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="45dc3-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="45dc3-284">Debe haber un `localhost` certificado con el `ASP.NET Core HTTPS development certificate` nombre descriptivo en `Current User > Personal > Certificates` y`Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="45dc3-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="45dc3-285">Quite todos los certificados encontrados de las entidades de certificación personales y de confianza.</span><span class="sxs-lookup"><span data-stu-id="45dc3-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="45dc3-286">**No** Quite el certificado de host local IIS Express.</span><span class="sxs-lookup"><span data-stu-id="45dc3-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="45dc3-287">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="45dc3-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="45dc3-288">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="45dc3-288">Close any browser instances open.</span></span> <span data-ttu-id="45dc3-289">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45dc3-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="45dc3-290">OS X: certificado no confiable</span><span class="sxs-lookup"><span data-stu-id="45dc3-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="45dc3-291">Abra el acceso a llaves.</span><span class="sxs-lookup"><span data-stu-id="45dc3-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="45dc3-292">Seleccione la cadena de claves del sistema.</span><span class="sxs-lookup"><span data-stu-id="45dc3-292">Select the System keychain.</span></span>
* <span data-ttu-id="45dc3-293">Compruebe la presencia de un certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="45dc3-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="45dc3-294">Compruebe que contiene un `+` símbolo en el icono para indicar que es de confianza para todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="45dc3-294">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="45dc3-295">Quite el certificado de la cadena de claves del sistema.</span><span class="sxs-lookup"><span data-stu-id="45dc3-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="45dc3-296">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="45dc3-296">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="45dc3-297">Cierre todas las instancias del explorador abiertas.</span><span class="sxs-lookup"><span data-stu-id="45dc3-297">Close any browser instances open.</span></span> <span data-ttu-id="45dc3-298">Abra una nueva ventana del explorador en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45dc3-298">Open a new browser window to app.</span></span>

<span data-ttu-id="45dc3-299">Vea [error de https mediante IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) para solucionar problemas de certificados con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45dc3-299">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="45dc3-300">IIS Express certificado SSL usado con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45dc3-300">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="45dc3-301">Para solucionar problemas con el certificado de IIS Express, seleccione **reparar** en el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45dc3-301">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="45dc3-302">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="45dc3-302">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="45dc3-303">Información adicional</span><span class="sxs-lookup"><span data-stu-id="45dc3-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="45dc3-304">Host ASP.NET Core en Linux con la configuración de Apache: HTTPS</span><span class="sxs-lookup"><span data-stu-id="45dc3-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="45dc3-305">Host ASP.NET Core en Linux con NGINX: configuración de HTTPS</span><span class="sxs-lookup"><span data-stu-id="45dc3-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="45dc3-306">Procedimientos para configurar SSL en IIS</span><span class="sxs-lookup"><span data-stu-id="45dc3-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="45dc3-307">Compatibilidad con el explorador de OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="45dc3-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
