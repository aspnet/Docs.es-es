---
title: Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga
author: rick-anderson
description: Aprenda sobre la configuración de las aplicaciones hospedadas detrás de los servidores proxy y equilibradores de carga, que con frecuencia ocultan información importante sobre las solicitudes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 75bb92908525d18af57f408e8ebba53c6eec88b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015522"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="70504-103">Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga</span><span class="sxs-lookup"><span data-stu-id="70504-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="70504-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="70504-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70504-105">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="70504-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="70504-106">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="70504-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="70504-107">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="70504-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="70504-108">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="70504-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="70504-109">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="70504-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="70504-110">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="70504-110">Forwarded headers</span></span>

<span data-ttu-id="70504-111">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="70504-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="70504-112">Header</span><span class="sxs-lookup"><span data-stu-id="70504-112">Header</span></span> | <span data-ttu-id="70504-113">Descripción</span><span class="sxs-lookup"><span data-stu-id="70504-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="70504-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="70504-114">X-Forwarded-For</span></span> | <span data-ttu-id="70504-115">Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="70504-116">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="70504-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="70504-117">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="70504-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="70504-118">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="70504-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="70504-119">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="70504-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="70504-120">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="70504-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="70504-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="70504-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="70504-122">El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="70504-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="70504-123">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="70504-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="70504-124">X-Forwarded-Host</span></span> | <span data-ttu-id="70504-125">El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="70504-125">The original value of the Host header field.</span></span> <span data-ttu-id="70504-126">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="70504-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="70504-127">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="70504-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="70504-128">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="70504-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="70504-129">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="70504-129">The middleware updates:</span></span>

* <span data-ttu-id="70504-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcala mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="70504-131">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="70504-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="70504-132">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="70504-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="70504-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="70504-135">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="70504-136">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="70504-136">The default settings are:</span></span>

* <span data-ttu-id="70504-137">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="70504-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="70504-138">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="70504-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="70504-139">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="70504-140">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="70504-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="70504-141">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="70504-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="70504-142">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="70504-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="70504-143">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="70504-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="70504-144">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70504-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="70504-145">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70504-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="70504-146">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="70504-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="70504-147">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="70504-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="70504-148">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="70504-149">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="70504-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="70504-150">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="70504-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="70504-151">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="70504-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="70504-152">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="70504-153">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="70504-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="70504-154">Orden del middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="70504-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="70504-155">El middleware de encabezados reenviados debe ejecutarse antes de otro middleware.</span><span class="sxs-lookup"><span data-stu-id="70504-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="70504-156">Hacerlo en ese orden garantiza que el middleware que se basa en la información de encabezados reenviados pueda usar los valores de encabezado para procesarlos.</span><span class="sxs-lookup"><span data-stu-id="70504-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="70504-157">El middleware de encabezados reenviados puede ejecutarse después del diagnóstico y el tratamiento de errores, pero siempre antes de llamar a `UseHsts`:</span><span class="sxs-lookup"><span data-stu-id="70504-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="70504-158">Como alternativa, llame a `UseForwardedHeaders` antes del diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="70504-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="70504-159">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="70504-160">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="70504-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="70504-161">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="70504-161">Nginx configuration</span></span>

<span data-ttu-id="70504-162">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="70504-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="70504-163">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="70504-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="70504-164">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="70504-164">Apache configuration</span></span>

<span data-ttu-id="70504-165">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="70504-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="70504-166">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="70504-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="70504-167">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="70504-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="70504-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="70504-169">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="70504-169">The following example changes the default values:</span></span>

* <span data-ttu-id="70504-170">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="70504-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="70504-171">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="70504-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="70504-172">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="70504-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="70504-173">Opción</span><span class="sxs-lookup"><span data-stu-id="70504-173">Option</span></span> | <span data-ttu-id="70504-174">Descripción</span><span class="sxs-lookup"><span data-stu-id="70504-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="70504-175">Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="70504-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="70504-176">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="70504-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="70504-177">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="70504-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="70504-178">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="70504-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="70504-179">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="70504-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="70504-180">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="70504-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="70504-181">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="70504-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="70504-182">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="70504-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="70504-183">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="70504-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="70504-184">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="70504-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="70504-185">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="70504-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="70504-186">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="70504-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="70504-187">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="70504-188">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-189">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="70504-190">Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="70504-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="70504-191">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="70504-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="70504-192">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="70504-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="70504-193">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="70504-194">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="70504-195">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-196">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="70504-197">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="70504-198">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-199">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="70504-200">Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="70504-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="70504-201">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="70504-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="70504-202">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="70504-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="70504-203">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="70504-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="70504-204">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="70504-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="70504-205">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="70504-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="70504-206">Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="70504-207">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="70504-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="70504-208">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="70504-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="70504-209">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-210">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="70504-211">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="70504-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="70504-212">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="70504-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="70504-213">Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="70504-214">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="70504-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="70504-215">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="70504-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="70504-216">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-217">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="70504-218">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="70504-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="70504-219">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="70504-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="70504-220">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="70504-221">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="70504-222">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="70504-223">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="70504-224">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="70504-225">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="70504-226">Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="70504-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="70504-227">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="70504-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="70504-228">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="70504-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="70504-229">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="70504-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="70504-230">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="70504-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="70504-231">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="70504-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="70504-232">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="70504-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="70504-233">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="70504-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="70504-234">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="70504-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="70504-235">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="70504-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="70504-236">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="70504-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="70504-237">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="70504-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="70504-238">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="70504-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="70504-239">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="70504-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="70504-240">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="70504-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="70504-241">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="70504-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="70504-242">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="70504-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="70504-243">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="70504-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="70504-244">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="70504-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="70504-245">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="70504-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="70504-246">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-247">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="70504-248">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="70504-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="70504-249">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="70504-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="70504-250">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="70504-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="70504-251">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="70504-251">Converted prefix length: 104</span></span>

<span data-ttu-id="70504-252">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="70504-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="70504-253">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="70504-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="70504-254">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="70504-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="70504-255">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="70504-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="70504-256">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="70504-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="70504-257">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="70504-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="70504-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="70504-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="70504-259">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="70504-260">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="70504-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="70504-261">Reenvío de certificados</span><span class="sxs-lookup"><span data-stu-id="70504-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="70504-262">Azure</span><span class="sxs-lookup"><span data-stu-id="70504-262">Azure</span></span>

<span data-ttu-id="70504-263">Para configurar Azure App Service para el reenvío de certificados, consulte [Configuración de la autenticación mutua de TLS en Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="70504-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="70504-264">La guía siguiente se aplica a la configuración de la aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70504-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="70504-265">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="70504-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="70504-266">Configure el middleware de reenvío de certificados para especificar el nombre de encabezado que Azure usa.</span><span class="sxs-lookup"><span data-stu-id="70504-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="70504-267">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="70504-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="70504-268">Otros servidores proxy web</span><span class="sxs-lookup"><span data-stu-id="70504-268">Other web proxies</span></span>

<span data-ttu-id="70504-269">Si se usa un proxy que no es IIS ni Enrutamiento de solicitud de aplicaciones de Azure App Service, configure el proxy para reenviar el certificado que recibió en un encabezado HTTP.</span><span class="sxs-lookup"><span data-stu-id="70504-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="70504-270">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="70504-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="70504-271">Configure el middleware de reenvío de certificados para especificar el nombre del encabezado.</span><span class="sxs-lookup"><span data-stu-id="70504-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="70504-272">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="70504-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="70504-273">Si el proxy no codifica en Base64 el certificado (como ocurre con Nginx), establezca la opción `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="70504-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="70504-274">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70504-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="70504-275">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="70504-275">Troubleshoot</span></span>

<span data-ttu-id="70504-276">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="70504-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="70504-277">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="70504-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="70504-278">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="70504-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="70504-279">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="70504-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="70504-280">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="70504-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="70504-281">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="70504-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="70504-282">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="70504-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="70504-283">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="70504-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="70504-284">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="70504-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="70504-285">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="70504-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="70504-286">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="70504-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="70504-287">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="70504-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="70504-288">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="70504-289">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="70504-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="70504-290">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="70504-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="70504-291">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="70504-292">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="70504-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70504-293">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="70504-294">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="70504-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="70504-295">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="70504-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70504-296">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="70504-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="70504-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="70504-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="70504-298">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="70504-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="70504-299">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="70504-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="70504-300">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="70504-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="70504-301">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="70504-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="70504-302">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="70504-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="70504-303">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="70504-303">Forwarded headers</span></span>

<span data-ttu-id="70504-304">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="70504-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="70504-305">Header</span><span class="sxs-lookup"><span data-stu-id="70504-305">Header</span></span> | <span data-ttu-id="70504-306">Descripción</span><span class="sxs-lookup"><span data-stu-id="70504-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="70504-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="70504-307">X-Forwarded-For</span></span> | <span data-ttu-id="70504-308">Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="70504-309">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="70504-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="70504-310">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="70504-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="70504-311">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="70504-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="70504-312">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="70504-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="70504-313">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="70504-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="70504-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="70504-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="70504-315">El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="70504-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="70504-316">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="70504-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="70504-317">X-Forwarded-Host</span></span> | <span data-ttu-id="70504-318">El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="70504-318">The original value of the Host header field.</span></span> <span data-ttu-id="70504-319">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="70504-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="70504-320">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="70504-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="70504-321">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="70504-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="70504-322">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="70504-322">The middleware updates:</span></span>

* <span data-ttu-id="70504-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcala mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="70504-324">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="70504-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="70504-325">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="70504-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="70504-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="70504-328">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="70504-329">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="70504-329">The default settings are:</span></span>

* <span data-ttu-id="70504-330">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="70504-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="70504-331">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="70504-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="70504-332">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="70504-333">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="70504-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="70504-334">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="70504-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="70504-335">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="70504-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="70504-336">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="70504-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="70504-337">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70504-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="70504-338">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70504-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="70504-339">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="70504-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="70504-340">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="70504-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="70504-341">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="70504-342">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="70504-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="70504-343">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="70504-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="70504-344">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="70504-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="70504-345">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="70504-346">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="70504-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70504-347">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure` antes de llamar a otro middleware:</span><span class="sxs-lookup"><span data-stu-id="70504-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="70504-348">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="70504-349">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="70504-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="70504-350">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="70504-350">Nginx configuration</span></span>

<span data-ttu-id="70504-351">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="70504-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="70504-352">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="70504-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="70504-353">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="70504-353">Apache configuration</span></span>

<span data-ttu-id="70504-354">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="70504-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="70504-355">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="70504-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="70504-356">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="70504-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="70504-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="70504-358">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="70504-358">The following example changes the default values:</span></span>

* <span data-ttu-id="70504-359">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="70504-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="70504-360">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="70504-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="70504-361">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="70504-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="70504-362">Opción</span><span class="sxs-lookup"><span data-stu-id="70504-362">Option</span></span> | <span data-ttu-id="70504-363">Descripción</span><span class="sxs-lookup"><span data-stu-id="70504-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="70504-364">Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="70504-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="70504-365">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="70504-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="70504-366">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="70504-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="70504-367">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="70504-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="70504-368">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="70504-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="70504-369">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="70504-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="70504-370">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="70504-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="70504-371">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="70504-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="70504-372">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="70504-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="70504-373">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="70504-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="70504-374">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="70504-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="70504-375">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="70504-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="70504-376">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="70504-377">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-378">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="70504-379">Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="70504-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="70504-380">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="70504-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="70504-381">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="70504-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="70504-382">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="70504-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="70504-383">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="70504-384">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-385">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="70504-386">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="70504-387">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="70504-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="70504-388">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="70504-389">Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="70504-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="70504-390">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="70504-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="70504-391">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="70504-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="70504-392">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="70504-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="70504-393">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="70504-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="70504-394">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="70504-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="70504-395">Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="70504-396">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="70504-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="70504-397">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="70504-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="70504-398">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-399">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="70504-400">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="70504-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="70504-401">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="70504-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="70504-402">Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="70504-403">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="70504-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="70504-404">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="70504-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="70504-405">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-406">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="70504-407">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="70504-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="70504-408">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="70504-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="70504-409">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="70504-410">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="70504-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="70504-411">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="70504-412">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="70504-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="70504-413">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="70504-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="70504-414">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="70504-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="70504-415">Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="70504-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="70504-416">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="70504-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="70504-417">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="70504-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="70504-418">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="70504-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="70504-419">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="70504-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="70504-420">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="70504-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="70504-421">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="70504-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="70504-422">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="70504-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="70504-423">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="70504-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="70504-424">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="70504-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="70504-425">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="70504-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="70504-426">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="70504-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="70504-427">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="70504-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="70504-428">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="70504-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="70504-429">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="70504-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="70504-430">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="70504-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="70504-431">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="70504-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="70504-432">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="70504-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="70504-433">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="70504-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="70504-434">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="70504-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="70504-435">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="70504-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="70504-436">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="70504-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="70504-437">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="70504-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="70504-438">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="70504-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="70504-439">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="70504-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="70504-440">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="70504-440">Converted prefix length: 104</span></span>

<span data-ttu-id="70504-441">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="70504-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="70504-442">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="70504-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="70504-443">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="70504-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="70504-444">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="70504-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="70504-445">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="70504-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="70504-446">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="70504-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="70504-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="70504-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="70504-448">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="70504-449">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="70504-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="70504-450">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="70504-450">Troubleshoot</span></span>

<span data-ttu-id="70504-451">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="70504-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="70504-452">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="70504-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="70504-453">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="70504-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="70504-454">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="70504-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="70504-455">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="70504-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="70504-456">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="70504-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="70504-457">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="70504-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="70504-458">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="70504-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="70504-459">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="70504-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="70504-460">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="70504-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="70504-461">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="70504-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="70504-462">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="70504-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="70504-463">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="70504-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="70504-464">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="70504-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="70504-465">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="70504-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="70504-466">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="70504-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="70504-467">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="70504-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70504-468">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="70504-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="70504-469">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="70504-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="70504-470">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="70504-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70504-471">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="70504-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="70504-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="70504-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
