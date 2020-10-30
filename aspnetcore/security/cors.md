---
title: Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo CORS como estándar para permitir o rechazar solicitudes entre orígenes en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060825"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="6379c-103">Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6379c-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6379c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6379c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="6379c-105">En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6379c-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="6379c-106">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="6379c-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6379c-107">Esta restricción se denomina *directiva de mismo origen* .</span><span class="sxs-lookup"><span data-stu-id="6379c-107">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="6379c-108">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="6379c-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6379c-109">En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="6379c-110">Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="6379c-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="6379c-111">[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="6379c-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="6379c-112">Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="6379c-113">**No** es una característica de seguridad, CORS reduce la seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="6379c-114">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="6379c-115">Para obtener más información, vea [Cómo funciona CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="6379c-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="6379c-116">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.</span><span class="sxs-lookup"><span data-stu-id="6379c-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="6379c-117">Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="6379c-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="6379c-118">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6379c-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="6379c-119">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="6379c-119">Same origin</span></span>

<span data-ttu-id="6379c-120">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="6379c-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="6379c-121">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="6379c-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="6379c-122">Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="6379c-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="6379c-123">`https://example.net`: Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="6379c-124">`https://www.example.com/foo.html`: Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="6379c-125">`http://example.com/foo.html`: Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="6379c-126">`https://example.com:9000/foo.html`: Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="6379c-127">Habilitación de CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-127">Enable CORS</span></span>

<span data-ttu-id="6379c-128">Hay tres maneras de habilitar CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="6379c-129">En middleware con una directiva [con nombre](#np) o una [directiva predeterminada](#dp).</span><span class="sxs-lookup"><span data-stu-id="6379c-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="6379c-130">Usar el [enrutamiento de puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="6379c-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="6379c-131">Con el atributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="6379c-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="6379c-132">El uso del atributo [[EnableCors]](#attr) con una directiva con nombre proporciona el control más fino para limitar los puntos de conexión que admiten CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="6379c-133"><xref:Owin.CorsExtensions.UseCors%2A> se debe llamar a antes de <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> usar `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="6379c-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="6379c-134">Cada enfoque se detalla en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="6379c-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="6379c-135">CORS con Directiva con nombre y middleware</span><span class="sxs-lookup"><span data-stu-id="6379c-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="6379c-136">Middleware de CORS controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="6379c-137">En el código siguiente se aplica una directiva de CORS a todos los puntos de conexión de la aplicación con los orígenes especificados:</span><span class="sxs-lookup"><span data-stu-id="6379c-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="6379c-138">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="6379c-138">The preceding code:</span></span>

* <span data-ttu-id="6379c-139">Establece el nombre de la Directiva en `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="6379c-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="6379c-140">El nombre de la Directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="6379c-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="6379c-141">Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión y especifica la  `_myAllowSpecificOrigins` Directiva CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="6379c-142">`UseCors` agrega el middleware de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="6379c-143">La llamada a `UseCors` debe colocarse después `UseRouting` de, pero antes de `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="6379c-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="6379c-144">Para obtener más información, vea [orden de middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="6379c-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="6379c-145">Llama a <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="6379c-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6379c-146">La expresión lambda toma un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="6379c-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="6379c-147">[Las opciones de configuración](#cors-policy-options), como `WithOrigins` , se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="6379c-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="6379c-148">Habilita la `_myAllowSpecificOrigins` Directiva CORS para todos los puntos de conexión del controlador.</span><span class="sxs-lookup"><span data-stu-id="6379c-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="6379c-149">Consulte [enrutamiento de puntos de conexión](#ecors) para aplicar una directiva de CORS a puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="6379c-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="6379c-150">Al usar el [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware), llame a antes de <xref:Owin.CorsExtensions.UseCors%2A> <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="6379c-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="6379c-151">Con el enrutamiento de punto de conexión, el middleware de CORS **debe** estar configurado para ejecutarse entre las llamadas a `UseRouting` y `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="6379c-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="6379c-152">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="6379c-153">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6379c-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="6379c-154">Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="6379c-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="6379c-155">Los <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos se pueden encadenar, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6379c-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="6379c-156">Nota: la dirección URL especificada **no** debe contener una barra diagonal final ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="6379c-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="6379c-157">Si la dirección URL termina con `/` , la comparación devuelve `false` y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="6379c-158">CORS con Directiva y middleware predeterminados</span><span class="sxs-lookup"><span data-stu-id="6379c-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="6379c-159">El siguiente código resaltado habilita la directiva predeterminada CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="6379c-160">El código anterior aplica la directiva predeterminada CORS a todos los puntos de conexión del controlador.</span><span class="sxs-lookup"><span data-stu-id="6379c-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="6379c-161">Habilitación de CORS con enrutamiento de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="6379c-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="6379c-162">La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite [solicitudes preparatorias automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="6379c-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="6379c-163">Para obtener más información, consulte [este problema de github](https://github.com/dotnet/aspnetcore/issues/20709) y [pruebe CORS con enrutamiento de puntos de conexión y [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="6379c-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="6379c-164">Con el enrutamiento de punto de conexión, CORS se puede habilitar en cada punto de conexión mediante el <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> conjunto de métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="6379c-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="6379c-165">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="6379c-165">In the preceding code:</span></span>

* <span data-ttu-id="6379c-166">`app.UseCors` habilita el middleware de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="6379c-167">Dado que no se ha configurado una directiva predeterminada, `app.UseCors()` solo no habilita CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="6379c-168">Los `/echo` puntos de conexión del controlador y permiten solicitudes entre orígenes mediante la Directiva especificada.</span><span class="sxs-lookup"><span data-stu-id="6379c-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="6379c-169">Los `/echo2` extremos de las :::no-loc(Razor)::: páginas y **no** permiten solicitudes entre orígenes porque no se especificó ninguna directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6379c-169">The `/echo2` and :::no-loc(Razor)::: Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="6379c-170">El atributo [[DisableCors]](#dc) **no**  deshabilita CORS habilitado por el enrutamiento de puntos de conexión con `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="6379c-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="6379c-171">Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar código similar al anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="6379c-172">Habilitación de CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="6379c-172">Enable CORS with attributes</span></span>

<span data-ttu-id="6379c-173">La habilitación de CORS con el atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) y la aplicación de una directiva con nombre solo a los puntos de conexión que requieren CORS proporciona el control más fino.</span><span class="sxs-lookup"><span data-stu-id="6379c-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="6379c-174">El atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="6379c-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="6379c-175">El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos:</span><span class="sxs-lookup"><span data-stu-id="6379c-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="6379c-176">`[EnableCors]` Especifica la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6379c-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="6379c-177">`[EnableCors("{Policy String}")]` especifica una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="6379c-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="6379c-178">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="6379c-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="6379c-179">:::no-loc(Razor)::: Del `PageModel`</span><span class="sxs-lookup"><span data-stu-id="6379c-179">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="6379c-180">Controller</span><span class="sxs-lookup"><span data-stu-id="6379c-180">Controller</span></span>
* <span data-ttu-id="6379c-181">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="6379c-181">Controller action method</span></span>

<span data-ttu-id="6379c-182">Se pueden aplicar directivas diferentes a los controladores, modelos de página o métodos de acción con el `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6379c-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="6379c-183">Cuando el `[EnableCors]` atributo se aplica a un controlador, un modelo de página o un método de acción y CORS está habilitado en middleware, se aplican **ambas** directivas.</span><span class="sxs-lookup"><span data-stu-id="6379c-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="6379c-184">**Se recomienda no combinar directivas. Use el** `[EnableCors]` **atributo o middleware, no ambos en la misma aplicación.**</span><span class="sxs-lookup"><span data-stu-id="6379c-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="6379c-185">En el código siguiente se aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="6379c-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="6379c-186">En el código siguiente se crean dos directivas de CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="6379c-187">Para el control más fino de la limitación de solicitudes de CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="6379c-188">Se usa `[EnableCors("MyPolicy")]` con una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="6379c-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="6379c-189">No defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6379c-189">Don't define a default policy.</span></span>
* <span data-ttu-id="6379c-190">No use el [enrutamiento de puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="6379c-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="6379c-191">El código de la sección siguiente se ajusta a la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="6379c-192">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="6379c-193">Deshabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-193">Disable CORS</span></span>

<span data-ttu-id="6379c-194">El atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **no**  deshabilita CORS habilitado por el enrutamiento de [puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="6379c-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="6379c-195">En el código siguiente se define la Directiva CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="6379c-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="6379c-196">El código siguiente deshabilita CORS para la `GetValues2` acción:</span><span class="sxs-lookup"><span data-stu-id="6379c-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="6379c-197">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="6379c-197">The preceding code:</span></span>

* <span data-ttu-id="6379c-198">No habilita CORS con [enrutamiento de punto de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="6379c-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="6379c-199">No define una [directiva predeterminada de CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="6379c-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="6379c-200">Usa [[EnableCors ("MyPolicy")]](#attr) para habilitar la `"MyPolicy"` Directiva CORS para el controlador.</span><span class="sxs-lookup"><span data-stu-id="6379c-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="6379c-201">Deshabilita CORS para el `GetValues2` método.</span><span class="sxs-lookup"><span data-stu-id="6379c-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="6379c-202">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="6379c-203">Opciones de directiva de CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-203">CORS policy options</span></span>

<span data-ttu-id="6379c-204">En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="6379c-205">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="6379c-206">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="6379c-207">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="6379c-208">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="6379c-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="6379c-209">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="6379c-210">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="6379c-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="6379c-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> se llama a en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6379c-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6379c-212">Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="6379c-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="6379c-213">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-213">Set the allowed origins</span></span>

<span data-ttu-id="6379c-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitudes de CORS de todos los orígenes con cualquier esquema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="6379c-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="6379c-215">`AllowAnyOrigin` no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="6379c-216">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="6379c-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6379c-217">El servicio CORS devuelve una respuesta de CORS no válida cuando se configura una aplicación con ambos métodos.</span><span class="sxs-lookup"><span data-stu-id="6379c-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="6379c-218">`AllowAnyOrigin` afecta a las solicitudes preparatorias y al `Access-Control-Allow-Origin` encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="6379c-219">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6379c-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="6379c-221">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="6379c-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="6379c-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="6379c-223">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="6379c-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="6379c-224">Afecta a las solicitudes preparatorias y al `Access-Control-Allow-Methods` encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="6379c-225">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="6379c-226">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-226">Set the allowed request headers</span></span>

<span data-ttu-id="6379c-227">Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados [encabezados de solicitud de autor](https://xhr.spec.whatwg.org/#request), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="6379c-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="6379c-228">Para permitir todos los [encabezados de solicitud de autor](https://www.w3.org/TR/cors/#author-request-headers), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="6379c-229">`AllowAnyHeader` afecta a las solicitudes preparatorias y al encabezado [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="6379c-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="6379c-230">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6379c-231">Una coincidencia de directiva de middleware de CORS con encabezados específicos especificados por `WithHeaders` solo es posible cuando los encabezados enviados `Access-Control-Request-Headers` coinciden exactamente con los encabezados indicados en `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="6379c-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="6379c-232">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="6379c-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="6379c-233">Middleware de CORS rechaza una solicitud preparatoria con el siguiente encabezado de solicitud porque `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) no aparece en `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="6379c-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="6379c-234">La aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6379c-235">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="6379c-236">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="6379c-236">Set the exposed response headers</span></span>

<span data-ttu-id="6379c-237">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="6379c-238">Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="6379c-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="6379c-239">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="6379c-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="6379c-240">La especificación CORS llama a estos encabezados de *respuesta simple* .</span><span class="sxs-lookup"><span data-stu-id="6379c-240">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="6379c-241">Para que otros encabezados estén disponibles para la aplicación, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="6379c-242">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="6379c-243">Las credenciales requieren un tratamiento especial en una solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="6379c-244">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="6379c-245">Las credenciales incluyen :::no-loc(cookie)::: esquemas de autenticación s y http.</span><span class="sxs-lookup"><span data-stu-id="6379c-245">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="6379c-246">Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true` .</span><span class="sxs-lookup"><span data-stu-id="6379c-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="6379c-247">Usar `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="6379c-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="6379c-248">Usar jQuery:</span><span class="sxs-lookup"><span data-stu-id="6379c-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="6379c-249">Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="6379c-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="6379c-250">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="6379c-250">The server must allow the credentials.</span></span> <span data-ttu-id="6379c-251">Para permitir las credenciales entre orígenes, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="6379c-252">La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="6379c-253">Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="6379c-254">Permitir las credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="6379c-255">Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="6379c-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="6379c-256">La especificación CORS también indica que el establecimiento de orígenes en `"*"` (todos los orígenes) no es válido si el `Access-Control-Allow-Credentials` encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="6379c-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="6379c-257">Solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="6379c-257">Preflight requests</span></span>

<span data-ttu-id="6379c-258">En algunas solicitudes de CORS, el explorador envía una solicitud [Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de efectuar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="6379c-259">Esta solicitud se denomina [solicitud preparatoria](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="6379c-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="6379c-260">El explorador puede omitir la solicitud preparatoria si se cumplen **todas** las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="6379c-261">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="6379c-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="6379c-262">La aplicación no establece encabezados de solicitud distintos de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="6379c-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="6379c-263">El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="6379c-264">La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la aplicación mediante una llamada a `setRequestHeader` en el `XMLHttpRequest` objeto.</span><span class="sxs-lookup"><span data-stu-id="6379c-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="6379c-265">La especificación CORS llama a estos encabezados de [solicitud Author](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="6379c-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="6379c-266">La regla no se aplica a los encabezados que el explorador puede establecer, como `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="6379c-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="6379c-267">A continuación se incluye una respuesta de ejemplo similar a la solicitud preparatoria realizada desde el botón **[Put Test]** de la sección [Test CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="6379c-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="6379c-268">La solicitud preparatoria usa el método [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="6379c-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="6379c-269">Puede incluir los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-269">It may include the following headers:</span></span>

* <span data-ttu-id="6379c-270">[Access-Control-request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): método HTTP que se utilizará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="6379c-271">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="6379c-272">Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="6379c-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="6379c-273">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="6379c-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="6379c-274">Si se deniega la solicitud preparatoria, la aplicación devuelve una `200 OK` respuesta pero no establece los encabezados CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="6379c-275">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="6379c-276">Para obtener un ejemplo de una solicitud preparatoria denegada, consulte la sección [prueba CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="6379c-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="6379c-277">Con las herramientas F12, la aplicación de consola muestra un error similar a uno de los siguientes, en función del explorador:</span><span class="sxs-lookup"><span data-stu-id="6379c-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="6379c-278">Firefox: solicitud entre orígenes bloqueada: la misma directiva de origen no permite la lectura del recurso remoto en `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="6379c-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="6379c-279">(Motivo: la solicitud de CORS no se realizó correctamente).</span><span class="sxs-lookup"><span data-stu-id="6379c-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="6379c-280">Más información</span><span class="sxs-lookup"><span data-stu-id="6379c-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="6379c-281">Basado en cromo: el acceso a la captura en ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' desde el origen ' ' se ha https://cors3.azurewebsites.net bloqueado mediante la Directiva de CORS: la respuesta a la solicitud preparatoria no pasa la comprobación de control de acceso: no hay ningún encabezado ' Access-Control-Allow-Origin ' presente en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="6379c-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="6379c-282">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="6379c-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="6379c-283">Para permitir encabezados específicos, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="6379c-284">Para permitir todos los [encabezados de solicitud de autor](https://www.w3.org/TR/cors/#author-request-headers), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="6379c-285">Los exploradores no son coherentes en el modo en que se establecen `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="6379c-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="6379c-286">Si:</span><span class="sxs-lookup"><span data-stu-id="6379c-286">If either:</span></span>

* <span data-ttu-id="6379c-287">Los encabezados se establecen en un valor distinto de `"*"`</span><span class="sxs-lookup"><span data-stu-id="6379c-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="6379c-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> se llama a: incluye al menos `Accept` , `Content-Type` y `Origin` , además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="6379c-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="6379c-289">Código de solicitud de comprobación previa automática</span><span class="sxs-lookup"><span data-stu-id="6379c-289">Automatic preflight request code</span></span>

<span data-ttu-id="6379c-290">Cuando se aplica la Directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="6379c-291">Globalmente llamando a `app.UseCors` en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6379c-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="6379c-292">Usar el `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6379c-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="6379c-293">ASP.NET Core responde a la solicitud de opciones preparatorias.</span><span class="sxs-lookup"><span data-stu-id="6379c-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="6379c-294">La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite solicitudes preparatorias automáticas.</span><span class="sxs-lookup"><span data-stu-id="6379c-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="6379c-295">En la sección [prueba CORS](#testc) de este documento se muestra este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="6379c-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="6379c-296">Atributo [HttpOptions] para solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="6379c-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="6379c-297">Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="6379c-298">En algunos escenarios, puede que este no sea el caso.</span><span class="sxs-lookup"><span data-stu-id="6379c-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="6379c-299">Por ejemplo, con [CORS con enrutamiento de punto de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="6379c-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="6379c-300">En el código siguiente se usa el atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para crear extremos para las solicitudes Options:</span><span class="sxs-lookup"><span data-stu-id="6379c-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="6379c-301">Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="6379c-302">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="6379c-302">Set the preflight expiration time</span></span>

<span data-ttu-id="6379c-303">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria.</span><span class="sxs-lookup"><span data-stu-id="6379c-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="6379c-304">Para establecer este encabezado, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="6379c-305">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-305">How CORS works</span></span>

<span data-ttu-id="6379c-306">En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.</span><span class="sxs-lookup"><span data-stu-id="6379c-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="6379c-307">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="6379c-308">CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="6379c-309">Por ejemplo, un actor malintencionado podría usar el [scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="6379c-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="6379c-310">Una API no es más segura, ya que permite CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="6379c-311">Es el cliente (explorador) el que debe aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="6379c-312">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="6379c-313">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="6379c-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="6379c-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="6379c-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="6379c-315">Postman</span><span class="sxs-lookup"><span data-stu-id="6379c-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="6379c-316">HttpClient de .NET</span><span class="sxs-lookup"><span data-stu-id="6379c-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="6379c-317">Un explorador web escribiendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="6379c-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="6379c-318">Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.</span><span class="sxs-lookup"><span data-stu-id="6379c-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="6379c-319">Los exploradores sin CORS no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="6379c-320">Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="6379c-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="6379c-321">JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="6379c-322">Los scripts pueden cargarse entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="6379c-323">La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="6379c-324">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="6379c-325">No es necesario el código personalizado de JavaScript para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="6379c-326">[Botón poner prueba](https://cors3.azurewebsites.net/test) en el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implementado</span><span class="sxs-lookup"><span data-stu-id="6379c-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="6379c-327">A continuación se presenta un ejemplo de una solicitud entre orígenes desde el botón de prueba [valores](https://cors3.azurewebsites.net/) a `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="6379c-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="6379c-328">El `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="6379c-328">The `Origin` header:</span></span>

* <span data-ttu-id="6379c-329">Proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="6379c-330">Es obligatorio y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="6379c-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="6379c-331">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="6379c-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="6379c-332">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="6379c-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="6379c-333">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="6379c-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="6379c-334">En `OPTIONS` las solicitudes, el servidor establece **Response headers** el `Access-Control-Allow-Origin: {allowed origin}` encabezado de encabezados de respuesta en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="6379c-335">Por ejemplo, la solicitud de botón [eliminar [EnableCors]](https://cors1.azurewebsites.net/test?number=2) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado `OPTIONS` contiene los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="6379c-336">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="6379c-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="6379c-337">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="6379c-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="6379c-338">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="6379c-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="6379c-339">En los **encabezados de respuesta** anteriores, el servidor establece el encabezado [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-339">In the preceding **Response headers** , the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="6379c-340">El `https://cors1.azurewebsites.net` valor de este encabezado coincide con el `Origin` encabezado de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="6379c-341">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> se llama a, `Access-Control-Allow-Origin: *` se devuelve el valor del carácter comodín.</span><span class="sxs-lookup"><span data-stu-id="6379c-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="6379c-342">`AllowAnyOrigin` permite cualquier origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="6379c-343">Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado.</span><span class="sxs-lookup"><span data-stu-id="6379c-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="6379c-344">En concreto, el explorador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="6379c-345">Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="6379c-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="6379c-346">Mostrar solicitudes de opciones</span><span class="sxs-lookup"><span data-stu-id="6379c-346">Display OPTIONS requests</span></span>

<span data-ttu-id="6379c-347">De forma predeterminada, los exploradores Chrome y Edge no muestran las solicitudes de opciones en la pestaña red de las herramientas F12.</span><span class="sxs-lookup"><span data-stu-id="6379c-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="6379c-348">Para mostrar las solicitudes OPTIONs en estos exploradores:</span><span class="sxs-lookup"><span data-stu-id="6379c-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="6379c-349">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="6379c-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="6379c-350">deshabilite la marca.</span><span class="sxs-lookup"><span data-stu-id="6379c-350">disable the flag.</span></span>
* <span data-ttu-id="6379c-351">Reiniciar.</span><span class="sxs-lookup"><span data-stu-id="6379c-351">restart.</span></span>

<span data-ttu-id="6379c-352">Firefox muestra las solicitudes de opciones de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6379c-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="6379c-353">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="6379c-353">CORS in IIS</span></span>

<span data-ttu-id="6379c-354">Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="6379c-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="6379c-355">Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="6379c-356">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-356">Test CORS</span></span>

<span data-ttu-id="6379c-357">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tiene código para probar CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="6379c-358">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="6379c-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="6379c-359">El ejemplo es un proyecto de API con :::no-loc(Razor)::: páginas agregadas:</span><span class="sxs-lookup"><span data-stu-id="6379c-359">The sample is an API project with :::no-loc(Razor)::: Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="6379c-360">`WithOrigins("https://localhost:<port>");` solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="6379c-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="6379c-361">A continuación se `ValuesController` proporcionan los puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="6379c-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="6379c-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) lo proporciona el paquete NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) y muestra información de ruta.</span><span class="sxs-lookup"><span data-stu-id="6379c-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="6379c-363">Pruebe el código de ejemplo anterior utilizando uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="6379c-364">Use la aplicación de ejemplo implementada en [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="6379c-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="6379c-365">No es necesario descargar el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="6379c-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="6379c-366">Ejecute el ejemplo con `dotnet run` el uso de la dirección URL predeterminada de `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="6379c-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="6379c-367">Ejecute el ejemplo desde Visual Studio con el puerto establecido en 44398 para una dirección URL de `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="6379c-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="6379c-368">Usar un explorador con las herramientas F12:</span><span class="sxs-lookup"><span data-stu-id="6379c-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="6379c-369">Seleccione el botón **valores** y revise los encabezados en la pestaña **red** .</span><span class="sxs-lookup"><span data-stu-id="6379c-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="6379c-370">Seleccione el botón **poner prueba** .</span><span class="sxs-lookup"><span data-stu-id="6379c-370">Select the **PUT test** button.</span></span> <span data-ttu-id="6379c-371">Consulte [solicitudes de visualización de opciones](#options) para obtener instrucciones sobre cómo mostrar la solicitud Options.</span><span class="sxs-lookup"><span data-stu-id="6379c-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="6379c-372">La **prueba Put** crea dos solicitudes, una solicitud preparatoria de opciones y la solicitud Put.</span><span class="sxs-lookup"><span data-stu-id="6379c-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="6379c-373">Seleccione el **`GetValues2 [DisableCors]`** botón para desencadenar una solicitud de CORS con errores.</span><span class="sxs-lookup"><span data-stu-id="6379c-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="6379c-374">Como se mencionó en el documento, la respuesta devuelve 200 Success, pero no se realiza la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="6379c-375">Seleccione la pestaña **consola** para ver el error de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="6379c-376">Dependiendo del explorador, se muestra un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="6379c-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="6379c-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'`La Directiva de CORS bloqueó el acceso a la captura desde el origen `'https://cors3.azurewebsites.net'` : no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="6379c-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="6379c-378">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="6379c-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="6379c-379">Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [rizo](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="6379c-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="6379c-380">Cuando se usa una herramienta, el origen de la solicitud especificada por el `Origin` encabezado debe ser diferente del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="6379c-381">Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="6379c-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="6379c-382">No es necesario que el middleware de CORS procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="6379c-383">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="6379c-384">El siguiente comando usa `curl` para emitir una solicitud Options con información:</span><span class="sxs-lookup"><span data-stu-id="6379c-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="6379c-385">Prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="6379c-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="6379c-386">La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite [solicitudes preparatorias automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="6379c-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="6379c-387">Considere el código siguiente, que usa el [enrutamiento de punto de conexión para habilitar CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="6379c-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="6379c-388">A continuación se `TodoItems1Controller` proporcionan los puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="6379c-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="6379c-389">Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=1) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado.</span><span class="sxs-lookup"><span data-stu-id="6379c-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="6379c-390">Los botones **Delete [EnableCors]** y **Get [EnableCors]** se ejecutan correctamente, ya que los extremos tienen `[EnableCors]` y responden a las solicitudes preparatorias.</span><span class="sxs-lookup"><span data-stu-id="6379c-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="6379c-391">Se produce un error en los otros puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="6379c-391">The other endpoints fails.</span></span> <span data-ttu-id="6379c-392">Se produce un error en el botón **Get** porque [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envía:</span><span class="sxs-lookup"><span data-stu-id="6379c-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="6379c-393">Lo siguiente `TodoItems2Controller` proporciona puntos de conexión similares, pero incluye código explícito para responder a las solicitudes de opciones:</span><span class="sxs-lookup"><span data-stu-id="6379c-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="6379c-394">Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=2) del ejemplo implementado.</span><span class="sxs-lookup"><span data-stu-id="6379c-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="6379c-395">En la lista desplegable **controlador** , seleccione **preparatoria** y después **establezca controlador** .</span><span class="sxs-lookup"><span data-stu-id="6379c-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller** .</span></span> <span data-ttu-id="6379c-396">Todas las llamadas a CORS a los `TodoItems2Controller` puntos de conexión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="6379c-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6379c-397">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6379c-397">Additional resources</span></span>

* [<span data-ttu-id="6379c-398">Uso compartido de recursos entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="6379c-399">Introducción al módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6379c-400">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6379c-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6379c-401">En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6379c-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="6379c-402">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="6379c-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6379c-403">Esta restricción se denomina *directiva de mismo origen* .</span><span class="sxs-lookup"><span data-stu-id="6379c-403">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="6379c-404">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="6379c-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6379c-405">En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="6379c-406">Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="6379c-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="6379c-407">[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="6379c-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="6379c-408">Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="6379c-409">**No** es una característica de seguridad, CORS reduce la seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="6379c-410">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="6379c-411">Para obtener más información, vea [Cómo funciona CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="6379c-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="6379c-412">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.</span><span class="sxs-lookup"><span data-stu-id="6379c-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="6379c-413">Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="6379c-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="6379c-414">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6379c-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="6379c-415">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="6379c-415">Same origin</span></span>

<span data-ttu-id="6379c-416">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="6379c-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="6379c-417">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="6379c-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="6379c-418">Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="6379c-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="6379c-419">`https://example.net`: Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="6379c-420">`https://www.example.com/foo.html`: Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="6379c-421">`http://example.com/foo.html`: Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="6379c-422">`https://example.com:9000/foo.html`: Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="6379c-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="6379c-423">Internet Explorer no tiene en cuenta el puerto al comparar los orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="6379c-424">CORS con Directiva con nombre y middleware</span><span class="sxs-lookup"><span data-stu-id="6379c-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="6379c-425">Middleware de CORS controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="6379c-426">El código siguiente habilita CORS para toda la aplicación con el origen especificado:</span><span class="sxs-lookup"><span data-stu-id="6379c-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="6379c-427">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="6379c-427">The preceding code:</span></span>

* <span data-ttu-id="6379c-428">Establece el nombre de la Directiva en " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="6379c-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="6379c-429">El nombre de la Directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="6379c-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="6379c-430">Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión, que habilita CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="6379c-431">Llama a <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="6379c-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6379c-432">La expresión lambda toma un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="6379c-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="6379c-433">[Las opciones de configuración](#cors-policy-options), como `WithOrigins` , se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="6379c-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="6379c-434">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6379c-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="6379c-435">Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="6379c-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="6379c-436">El <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método puede encadenar métodos, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6379c-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="6379c-437">Nota: la dirección URL **no** debe contener una barra diagonal final ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="6379c-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="6379c-438">Si la dirección URL termina con `/` , la comparación devuelve `false` y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="6379c-439">El código siguiente aplica las directivas de CORS a todos los puntos de conexión de aplicaciones a través del middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="6379c-440">Nota: `UseCors` se debe llamar a antes de `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="6379c-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="6379c-441">Consulte [habilitación de CORS en :::no-loc(Razor)::: páginas, controladores y métodos de acción](#ecors) para aplicar la Directiva CORS en el nivel de página/controlador/acción.</span><span class="sxs-lookup"><span data-stu-id="6379c-441">See [Enable CORS in :::no-loc(Razor)::: Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="6379c-442">Consulte [prueba de CORS](#test) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="6379c-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="6379c-443">Habilitación de CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="6379c-443">Enable CORS with attributes</span></span>

<span data-ttu-id="6379c-444">El atributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="6379c-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="6379c-445">El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos.</span><span class="sxs-lookup"><span data-stu-id="6379c-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="6379c-446">`[EnableCors]`Se usa para especificar la directiva predeterminada y `[EnableCors("{Policy String}")]` para especificar una directiva.</span><span class="sxs-lookup"><span data-stu-id="6379c-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="6379c-447">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="6379c-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="6379c-448">:::no-loc(Razor)::: Del `PageModel`</span><span class="sxs-lookup"><span data-stu-id="6379c-448">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="6379c-449">Controller</span><span class="sxs-lookup"><span data-stu-id="6379c-449">Controller</span></span>
* <span data-ttu-id="6379c-450">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="6379c-450">Controller action method</span></span>

<span data-ttu-id="6379c-451">Puede aplicar diferentes directivas al controlador/página-modelo o acción con el  `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6379c-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="6379c-452">Cuando el `[EnableCors]` atributo se aplica a un método de acción/modelo de página/controladores y CORS está habilitado en middleware, se aplican **ambas** directivas.</span><span class="sxs-lookup"><span data-stu-id="6379c-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="6379c-453">Se recomienda **no** combinar directivas.</span><span class="sxs-lookup"><span data-stu-id="6379c-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="6379c-454">Use el `[EnableCors]` atributo o middleware, **no ambos** .</span><span class="sxs-lookup"><span data-stu-id="6379c-454">Use the `[EnableCors]` attribute or middleware, **not both** .</span></span> <span data-ttu-id="6379c-455">Al usar `[EnableCors]` , **no** defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6379c-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="6379c-456">En el código siguiente se aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="6379c-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="6379c-457">En el código siguiente se crea una directiva predeterminada de CORS y una directiva denominada `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="6379c-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="6379c-458">Deshabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-458">Disable CORS</span></span>

<span data-ttu-id="6379c-459">El atributo [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deshabilita CORS para el controlador/página-modelo/acción.</span><span class="sxs-lookup"><span data-stu-id="6379c-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="6379c-460">Opciones de directiva de CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-460">CORS policy options</span></span>

<span data-ttu-id="6379c-461">En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="6379c-462">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="6379c-463">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="6379c-464">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="6379c-465">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="6379c-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="6379c-466">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="6379c-467">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="6379c-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="6379c-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> se llama a en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6379c-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6379c-469">Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="6379c-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="6379c-470">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-470">Set the allowed origins</span></span>

<span data-ttu-id="6379c-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitudes de CORS de todos los orígenes con cualquier esquema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="6379c-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="6379c-472">`AllowAnyOrigin` no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="6379c-473">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="6379c-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6379c-474">En el caso de una aplicación segura, especifique una lista exacta de orígenes si el cliente debe autorizarse para obtener acceso a los recursos del servidor.</span><span class="sxs-lookup"><span data-stu-id="6379c-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="6379c-475">`AllowAnyOrigin` afecta a las solicitudes preparatorias y al `Access-Control-Allow-Origin` encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="6379c-476">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6379c-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="6379c-478">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="6379c-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="6379c-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="6379c-480">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="6379c-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="6379c-481">Afecta a las solicitudes preparatorias y al `Access-Control-Allow-Methods` encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="6379c-482">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="6379c-483">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="6379c-483">Set the allowed request headers</span></span>

<span data-ttu-id="6379c-484">Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados *encabezados de solicitud de autor* , llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="6379c-484">To allow specific headers to be sent in a CORS request, called *author request headers* , call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6379c-485">Para permitir todos los encabezados de solicitud de autor, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6379c-486">Esta configuración afecta a las solicitudes preparatorias y al `Access-Control-Request-Headers` encabezado.</span><span class="sxs-lookup"><span data-stu-id="6379c-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="6379c-487">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="6379c-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6379c-488">El middleware de CORS siempre permite `Access-Control-Request-Headers` que se envíen cuatro encabezados en el, independientemente de los valores configurados en CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="6379c-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="6379c-489">Esta lista de encabezados incluye:</span><span class="sxs-lookup"><span data-stu-id="6379c-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="6379c-490">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="6379c-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="6379c-491">Middleware de CORS responde correctamente a una solicitud preparatoria con el siguiente encabezado de solicitud porque `Content-Language` siempre se permite:</span><span class="sxs-lookup"><span data-stu-id="6379c-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="6379c-492">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="6379c-492">Set the exposed response headers</span></span>

<span data-ttu-id="6379c-493">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="6379c-494">Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="6379c-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="6379c-495">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="6379c-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="6379c-496">La especificación CORS llama a estos encabezados de *respuesta simple* .</span><span class="sxs-lookup"><span data-stu-id="6379c-496">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="6379c-497">Para que otros encabezados estén disponibles para la aplicación, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="6379c-498">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="6379c-499">Las credenciales requieren un tratamiento especial en una solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="6379c-500">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="6379c-501">Las credenciales incluyen :::no-loc(cookie)::: esquemas de autenticación s y http.</span><span class="sxs-lookup"><span data-stu-id="6379c-501">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="6379c-502">Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true` .</span><span class="sxs-lookup"><span data-stu-id="6379c-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="6379c-503">Usar `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="6379c-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="6379c-504">Usar jQuery:</span><span class="sxs-lookup"><span data-stu-id="6379c-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="6379c-505">Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="6379c-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="6379c-506">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="6379c-506">The server must allow the credentials.</span></span> <span data-ttu-id="6379c-507">Para permitir las credenciales entre orígenes, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="6379c-508">La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="6379c-509">Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="6379c-510">Permitir las credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="6379c-511">Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="6379c-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="6379c-512">La especificación CORS también indica que el establecimiento de orígenes en `"*"` (todos los orígenes) no es válido si el `Access-Control-Allow-Credentials` encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="6379c-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="6379c-513">Solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="6379c-513">Preflight requests</span></span>

<span data-ttu-id="6379c-514">En algunas solicitudes de CORS, el explorador envía una solicitud adicional antes de efectuar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="6379c-515">Esta solicitud se denomina *solicitud preparatoria* .</span><span class="sxs-lookup"><span data-stu-id="6379c-515">This request is called a *preflight request* .</span></span> <span data-ttu-id="6379c-516">El explorador puede omitir la solicitud preparatoria si se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="6379c-517">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="6379c-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="6379c-518">La aplicación no establece encabezados de solicitud distintos de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="6379c-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="6379c-519">El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="6379c-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="6379c-520">La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la aplicación mediante una llamada a `setRequestHeader` en el `XMLHttpRequest` objeto.</span><span class="sxs-lookup"><span data-stu-id="6379c-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="6379c-521">La especificación CORS llama a estos encabezados de *solicitud Author* .</span><span class="sxs-lookup"><span data-stu-id="6379c-521">The CORS specification calls these headers *author request headers* .</span></span> <span data-ttu-id="6379c-522">La regla no se aplica a los encabezados que el explorador puede establecer, como `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="6379c-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="6379c-523">El siguiente es un ejemplo de una solicitud preparatoria:</span><span class="sxs-lookup"><span data-stu-id="6379c-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="6379c-524">La solicitud previa al vuelo usa el método HTTP OPTIONs.</span><span class="sxs-lookup"><span data-stu-id="6379c-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="6379c-525">Incluye dos encabezados especiales:</span><span class="sxs-lookup"><span data-stu-id="6379c-525">It includes two special headers:</span></span>

* <span data-ttu-id="6379c-526">`Access-Control-Request-Method`: Método HTTP que se usará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="6379c-527">`Access-Control-Request-Headers`: Una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="6379c-528">Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="6379c-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="6379c-529">Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="6379c-530">Vea el [atributo [HttpOptions] para solicitudes preparatorias](#pro).</span><span class="sxs-lookup"><span data-stu-id="6379c-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="6379c-531">Una solicitud preparatoria de CORS podría incluir un `Access-Control-Request-Headers` encabezado, que indica al servidor los encabezados que se envían con la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="6379c-532">Para permitir encabezados específicos, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6379c-533">Para permitir todos los encabezados de solicitud de autor, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6379c-534">Los exploradores no son totalmente coherentes en el modo en que se establecen `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="6379c-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="6379c-535">Si establece encabezados en un valor distinto de `"*"` (o use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), debe incluir al menos `Accept` , `Content-Type` y `Origin` , además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="6379c-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="6379c-536">A continuación se ofrece un ejemplo de respuesta a la solicitud preparatoria (siempre que el servidor permita la solicitud):</span><span class="sxs-lookup"><span data-stu-id="6379c-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="6379c-537">La respuesta incluye un `Access-Control-Allow-Methods` encabezado que enumera los métodos permitidos y, opcionalmente `Access-Control-Allow-Headers` , un encabezado, que enumera los encabezados permitidos.</span><span class="sxs-lookup"><span data-stu-id="6379c-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="6379c-538">Si la solicitud preparatoria se realiza correctamente, el explorador envía la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="6379c-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="6379c-539">Si se deniega la solicitud preparatoria, la aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6379c-540">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="6379c-541">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="6379c-541">Set the preflight expiration time</span></span>

<span data-ttu-id="6379c-542">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria.</span><span class="sxs-lookup"><span data-stu-id="6379c-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="6379c-543">Para establecer este encabezado, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="6379c-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="6379c-544">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-544">How CORS works</span></span>

<span data-ttu-id="6379c-545">En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.</span><span class="sxs-lookup"><span data-stu-id="6379c-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="6379c-546">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="6379c-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="6379c-547">CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="6379c-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="6379c-548">Por ejemplo, un actor malintencionado podría usar [impedir el scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="6379c-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="6379c-549">La API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="6379c-550">Es el cliente (explorador) el que debe aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="6379c-551">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="6379c-552">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="6379c-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="6379c-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="6379c-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="6379c-554">Postman</span><span class="sxs-lookup"><span data-stu-id="6379c-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="6379c-555">HttpClient de .NET</span><span class="sxs-lookup"><span data-stu-id="6379c-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="6379c-556">Un explorador web escribiendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="6379c-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="6379c-557">Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.</span><span class="sxs-lookup"><span data-stu-id="6379c-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="6379c-558">Los exploradores (sin CORS) no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="6379c-559">Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="6379c-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="6379c-560">JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="6379c-561">Los scripts pueden cargarse entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="6379c-562">La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="6379c-563">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="6379c-564">No es necesario el código personalizado de JavaScript para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="6379c-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="6379c-565">A continuación se presenta un ejemplo de una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="6379c-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="6379c-566">El `Origin` encabezado proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="6379c-567">El `Origin` encabezado es obligatorio y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="6379c-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="6379c-568">Si el servidor permite la solicitud, establece el `Access-Control-Allow-Origin` encabezado en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="6379c-569">El valor de este encabezado coincide con el `Origin` encabezado de la solicitud o con el valor comodín `"*"` , lo que significa que se permite cualquier origen:</span><span class="sxs-lookup"><span data-stu-id="6379c-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="6379c-570">Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado.</span><span class="sxs-lookup"><span data-stu-id="6379c-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="6379c-571">En concreto, el explorador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="6379c-572">Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="6379c-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="6379c-573">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-573">Test CORS</span></span>

<span data-ttu-id="6379c-574">Para probar CORS:</span><span class="sxs-lookup"><span data-stu-id="6379c-574">To test CORS:</span></span>

1. <span data-ttu-id="6379c-575">[Cree un proyecto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="6379c-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="6379c-576">Como alternativa, puede [descargar el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="6379c-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="6379c-577">Habilite CORS con uno de los enfoques de este documento.</span><span class="sxs-lookup"><span data-stu-id="6379c-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="6379c-578">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6379c-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="6379c-579">`WithOrigins("https://localhost:<port>");` solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="6379c-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="6379c-580">Cree un proyecto de aplicación web ( :::no-loc(Razor)::: páginas o MVC).</span><span class="sxs-lookup"><span data-stu-id="6379c-580">Create a web app project (:::no-loc(Razor)::: Pages or MVC).</span></span> <span data-ttu-id="6379c-581">El ejemplo utiliza :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="6379c-581">The sample uses :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="6379c-582">Puede crear la aplicación web en la misma solución que el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="6379c-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="6379c-583">Agregue el siguiente código resaltado al archivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6379c-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="6379c-584">En el código anterior, reemplace `url: 'https://<web app>.azurewebsites.net/api/values/1',` por la dirección URL a la aplicación implementada.</span><span class="sxs-lookup"><span data-stu-id="6379c-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="6379c-585">Implemente el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="6379c-585">Deploy the API project.</span></span> <span data-ttu-id="6379c-586">Por ejemplo, [implemente en Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="6379c-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="6379c-587">Ejecute las :::no-loc(Razor)::: páginas o la aplicación MVC desde el escritorio y haga clic en el botón **probar** .</span><span class="sxs-lookup"><span data-stu-id="6379c-587">Run the :::no-loc(Razor)::: Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="6379c-588">Use las herramientas F12 para revisar los mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="6379c-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="6379c-589">Quite el origen localhost de `WithOrigins` e implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="6379c-590">Como alternativa, ejecute la aplicación cliente con un puerto diferente.</span><span class="sxs-lookup"><span data-stu-id="6379c-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="6379c-591">Por ejemplo, ejecute desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6379c-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="6379c-592">Pruebe con la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="6379c-592">Test with the client app.</span></span> <span data-ttu-id="6379c-593">Los errores de CORS devuelven un error, pero el mensaje de error no está disponible para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6379c-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="6379c-594">Use la pestaña consola de las herramientas F12 para ver el error.</span><span class="sxs-lookup"><span data-stu-id="6379c-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="6379c-595">Dependiendo del explorador, obtendrá un error (en la consola de herramientas de F12) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="6379c-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="6379c-596">Uso de Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="6379c-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="6379c-597">**SEC7120: [CORS] el origen `https://localhost:44375` no se encontró `https://localhost:44375` en el encabezado de respuesta Access-Control-Allow-Origin para el recurso entre orígenes en `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="6379c-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="6379c-598">Usar Chrome:</span><span class="sxs-lookup"><span data-stu-id="6379c-598">Using Chrome:</span></span>

     <span data-ttu-id="6379c-599">**`https://webapi.azurewebsites.net/api/values/1`La Directiva de CORS ha bloqueado el acceso a XMLHttpRequest desde el origen `https://localhost:44375` : no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="6379c-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="6379c-600">Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="6379c-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="6379c-601">Cuando se usa una herramienta, el origen de la solicitud especificada por el `Origin` encabezado debe ser diferente del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="6379c-602">Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="6379c-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="6379c-603">No es necesario que el middleware de CORS procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6379c-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="6379c-604">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6379c-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="6379c-605">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="6379c-605">CORS in IIS</span></span>

<span data-ttu-id="6379c-606">Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="6379c-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="6379c-607">Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6379c-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6379c-608">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6379c-608">Additional resources</span></span>

* [<span data-ttu-id="6379c-609">Uso compartido de recursos entre orígenes</span><span class="sxs-lookup"><span data-stu-id="6379c-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="6379c-610">Introducción al módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="6379c-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
