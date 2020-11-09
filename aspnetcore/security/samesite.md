---
title: 'Trabajar con SameSite cookie s en ASP.net Core'
author: rick-anderson
description: 'Aprenda a usar SameSite cookie s en ASP.net Core'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'Electron'
uid: security/samesite
ms.openlocfilehash: 6f826416e3045df32abf41e94e667120e71ae717
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051621"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="832aa-103">Trabajar con SameSite cookie s en ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="832aa-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="832aa-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="832aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="832aa-105">SameSite es un estándar de borrador de [IETF](https://ietf.org/about/) diseñado para proporcionar protección contra los ataques de falsificación de solicitud entre sitios (CSRF).</span><span class="sxs-lookup"><span data-stu-id="832aa-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="832aa-106">Originalmente elaborado en [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), el borrador estándar se actualizó en [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="832aa-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="832aa-107">El estándar actualizado no es compatible con versiones anteriores del estándar anterior, y las diferencias más destacables son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="832aa-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="832aa-108">Cookies sin el encabezado SameSite se tratan como `SameSite=Lax` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="832aa-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="832aa-109">`SameSite=None` debe usarse para permitir el uso entre sitios cookie .</span><span class="sxs-lookup"><span data-stu-id="832aa-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="832aa-110">Cookies que la aserción `SameSite=None` también debe marcarse como `Secure` .</span><span class="sxs-lookup"><span data-stu-id="832aa-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="832aa-111">Las aplicaciones que usan [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) pueden experimentar problemas con `sameSite=Lax` o `sameSite=Strict` cookie , ya que `<iframe>` se tratan como escenarios entre sitios.</span><span class="sxs-lookup"><span data-stu-id="832aa-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="832aa-112">El `SameSite=None` [estándar 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) no permite el valor y hace que algunas implementaciones traten tales cookie como `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="832aa-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="832aa-113">Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="832aa-114">La `SameSite=Lax` configuración funciona con la mayoría de las aplicaciones cookie .</span><span class="sxs-lookup"><span data-stu-id="832aa-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="832aa-115">Algunas formas de autenticación como [OpenID Connect](https://openid.net/connect/) (OIDC) y [WS-Federation](https://auth0.com/docs/protocols/ws-fed) tienen como valor predeterminado el envío de redirecciones basadas en post.</span><span class="sxs-lookup"><span data-stu-id="832aa-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="832aa-116">Las redirecciones basadas en POST desencadenan las protecciones del explorador de SameSite, por lo que SameSite está deshabilitado para estos componentes.</span><span class="sxs-lookup"><span data-stu-id="832aa-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="832aa-117">La mayoría de los inicios de sesión de [OAuth](https://oauth.net/) no se ven afectados debido a las diferencias en el modo en que fluye la solicitud.</span><span class="sxs-lookup"><span data-stu-id="832aa-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="832aa-118">Cada componente de ASP.NET Core que emite cookie s necesita decidir si SameSite es adecuado.</span><span class="sxs-lookup"><span data-stu-id="832aa-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="832aa-119">SameSite y Identity</span><span class="sxs-lookup"><span data-stu-id="832aa-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="832aa-120">Código de ejemplo de prueba de SameSite</span><span class="sxs-lookup"><span data-stu-id="832aa-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="832aa-121">Los ejemplos siguientes se pueden descargar y probar:</span><span class="sxs-lookup"><span data-stu-id="832aa-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="832aa-122">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="832aa-122">Sample</span></span>               | <span data-ttu-id="832aa-123">Documento</span><span class="sxs-lookup"><span data-stu-id="832aa-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="832aa-124">MVC de .NET Core</span><span class="sxs-lookup"><span data-stu-id="832aa-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="832aa-125">[Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="832aa-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="832aa-126">El ejemplo siguiente se puede descargar y probar:</span><span class="sxs-lookup"><span data-stu-id="832aa-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="832aa-127">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="832aa-127">Sample</span></span>               | <span data-ttu-id="832aa-128">Documento</span><span class="sxs-lookup"><span data-stu-id="832aa-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="832aa-129">[Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="832aa-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="832aa-130">Compatibilidad de .NET Core con el atributo sameSite</span><span class="sxs-lookup"><span data-stu-id="832aa-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="832aa-131">.NET Core 2,2 y versiones posteriores admiten el borrador estándar 2019 de SameSite desde la publicación de actualizaciones en diciembre de 2019.</span><span class="sxs-lookup"><span data-stu-id="832aa-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="832aa-132">Los desarrolladores pueden controlar mediante programación el valor del atributo sameSite mediante la `HttpCookie.SameSite` propiedad.</span><span class="sxs-lookup"><span data-stu-id="832aa-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="832aa-133">Si se establece la `SameSite` propiedad en Strict, LAX o None, los valores se escriben en la red con cookie .</span><span class="sxs-lookup"><span data-stu-id="832aa-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="832aa-134">Si se establece en, `(SameSiteMode)(-1)` indica que no se debe incluir ningún atributo sameSite en la red con el parámetro cookie</span><span class="sxs-lookup"><span data-stu-id="832aa-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="832aa-135">.NET Core 3,0 y versiones posteriores admiten los valores de SameSite actualizados y agrega un valor de enumeración adicional `SameSiteMode.Unspecified` a la `SameSiteMode` enumeración.</span><span class="sxs-lookup"><span data-stu-id="832aa-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="832aa-136">Este nuevo valor indica que no se debe enviar ningún sameSite con cookie .</span><span class="sxs-lookup"><span data-stu-id="832aa-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="832aa-137">Cambios de comportamiento de revisión de diciembre</span><span class="sxs-lookup"><span data-stu-id="832aa-137">December patch behavior changes</span></span>

<span data-ttu-id="832aa-138">El cambio de comportamiento específico para .NET Framework y .NET Core 2,1 es el modo en que la `SameSite` propiedad interpreta el `None` valor.</span><span class="sxs-lookup"><span data-stu-id="832aa-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="832aa-139">Antes de que la revisión sea un valor de `None` pensado "no emitir el atributo en absoluto", después de la revisión significa "emitir el atributo con un valor de `None` ".</span><span class="sxs-lookup"><span data-stu-id="832aa-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="832aa-140">Después de que la revisión sea un `SameSite` valor de `(SameSiteMode)(-1)` , no se emitirá el atributo.</span><span class="sxs-lookup"><span data-stu-id="832aa-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="832aa-141">El valor predeterminado de SameSite para la autenticación de formularios y el estado cookie de sesión s se cambió de `None` a `Lax` .</span><span class="sxs-lookup"><span data-stu-id="832aa-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="832aa-142">Uso de la API con SameSite</span><span class="sxs-lookup"><span data-stu-id="832aa-142">API usage with SameSite</span></span>

<span data-ttu-id="832aa-143">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) tiene como valor predeterminado `Unspecified` , lo que significa que no se ha agregado ningún atributo SameSite a cookie y el cliente usará su comportamiento predeterminado (LAX para los nuevos exploradores, ninguno para los antiguos).</span><span class="sxs-lookup"><span data-stu-id="832aa-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="832aa-144">En el código siguiente se muestra cómo cambiar el cookie valor de SameSite a `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="832aa-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="832aa-145">Todos los componentes de ASP.NET Core que emiten cookie s invalidan los valores predeterminados anteriores con la configuración adecuada para sus escenarios.</span><span class="sxs-lookup"><span data-stu-id="832aa-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="832aa-146">Los valores predeterminados anteriores invalidados no han cambiado.</span><span class="sxs-lookup"><span data-stu-id="832aa-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="832aa-147">Componente</span><span class="sxs-lookup"><span data-stu-id="832aa-147">Component</span></span> | cookie | <span data-ttu-id="832aa-148">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="832aa-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="832aa-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="832aa-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="832aa-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="832aa-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="832aa-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="832aa-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="832aa-152">[Cookie Autenticación](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="832aa-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="832aa-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="832aa-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="832aa-154">[TwitterOptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="832aa-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="832aa-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="832aa-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="832aa-156">[OpenIdConnectOptions. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="832aa-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="832aa-157">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="832aa-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="832aa-158">ASP.NET Core 3,1 y versiones posteriores proporcionan la siguiente compatibilidad con SameSite:</span><span class="sxs-lookup"><span data-stu-id="832aa-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="832aa-159">Vuelve a definir el comportamiento de `SameSiteMode.None` para emitir. `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="832aa-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="832aa-160">Agrega un nuevo valor `SameSiteMode.Unspecified` para omitir el atributo SameSite.</span><span class="sxs-lookup"><span data-stu-id="832aa-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="832aa-161">Todas las cookie API de tienen como valor predeterminado `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="832aa-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="832aa-162">Algunos componentes que utilizan cookie s establecen valores más específicos de sus escenarios.</span><span class="sxs-lookup"><span data-stu-id="832aa-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="832aa-163">Vea la tabla anterior para obtener ejemplos.</span><span class="sxs-lookup"><span data-stu-id="832aa-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="832aa-164">En ASP.NET Core 3,0 y versiones posteriores, se cambiaron los valores predeterminados de SameSite para evitar conflictos con los valores predeterminados de cliente incoherentes.</span><span class="sxs-lookup"><span data-stu-id="832aa-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="832aa-165">Las siguientes API han cambiado el valor predeterminado de `SameSiteMode.Lax ` a `-1` para evitar la emisión de un atributo SameSite para estos cookie s:</span><span class="sxs-lookup"><span data-stu-id="832aa-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="832aa-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> se usa con [HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="832aa-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="832aa-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  se usa como generador para `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="832aa-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="832aa-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="832aa-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="832aa-169">Historial y cambios</span><span class="sxs-lookup"><span data-stu-id="832aa-169">History and changes</span></span>

<span data-ttu-id="832aa-170">La compatibilidad con SameSite se implementó por primera vez en ASP.NET Core en 2,0 con el [borrador estándar de 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="832aa-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="832aa-171">El estándar 2016 era participación.</span><span class="sxs-lookup"><span data-stu-id="832aa-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="832aa-172">ASP.NET Core participar estableciendo varias cookie s en de `Lax` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="832aa-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="832aa-173">Después de encontrar varios [problemas](https://github.com/aspnet/Announcements/issues/318) con la autenticación, se [deshabilitó](https://github.com/aspnet/Announcements/issues/348)la mayor parte del uso de SameSite.</span><span class="sxs-lookup"><span data-stu-id="832aa-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="832aa-174">Las [revisiones](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) se emitieron en noviembre de 2019 para actualizar desde el estándar 2016 al estándar 2019.</span><span class="sxs-lookup"><span data-stu-id="832aa-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="832aa-175">El [borrador 2019 de la especificación SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="832aa-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="832aa-176">**No** es compatible con las versiones anteriores del borrador 2016.</span><span class="sxs-lookup"><span data-stu-id="832aa-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="832aa-177">Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="832aa-178">Especifica cookie que se tratan como `SameSite=Lax` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="832aa-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="832aa-179">Especifica cookie s que la aserción explícitamente `SameSite=None` para habilitar la entrega entre sitios debe marcarse como `Secure` .</span><span class="sxs-lookup"><span data-stu-id="832aa-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="832aa-180">`None` es una nueva entrada que se va a dejar de participar.</span><span class="sxs-lookup"><span data-stu-id="832aa-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="832aa-181">Es compatible con las revisiones emitidas para ASP.NET Core 2,1, 2,2 y 3,0.</span><span class="sxs-lookup"><span data-stu-id="832aa-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="832aa-182">ASP.NET Core 3,1 tiene compatibilidad adicional con SameSite.</span><span class="sxs-lookup"><span data-stu-id="832aa-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="832aa-183">Está programado para que [Chrome](https://chromestatus.com/feature/5088147346030592) lo habilite de forma predeterminada en [febrero de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="832aa-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="832aa-184">Los exploradores empezaron a pasar a este estándar en 2019.</span><span class="sxs-lookup"><span data-stu-id="832aa-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="832aa-185">API afectadas por el cambio del estándar de borrador de 2016 SameSite al borrador estándar de 2019</span><span class="sxs-lookup"><span data-stu-id="832aa-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="832aa-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="832aa-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="832aa-187">[CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="832aa-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="832aa-188">[CookieGenerador. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="832aa-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="832aa-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="832aa-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="832aa-190">Compatibilidad con exploradores más antiguos</span><span class="sxs-lookup"><span data-stu-id="832aa-190">Supporting older browsers</span></span>

<span data-ttu-id="832aa-191">El estándar 2016 SameSite impediba que los valores desconocidos se deben tratar como `SameSite=Strict` valores.</span><span class="sxs-lookup"><span data-stu-id="832aa-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="832aa-192">Las aplicaciones a las que se accede desde exploradores más antiguos que admiten el estándar 2016 SameSite se pueden interrumpir cuando obtienen una propiedad SameSite con un valor de `None` .</span><span class="sxs-lookup"><span data-stu-id="832aa-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="832aa-193">Las aplicaciones web deben implementar la detección del explorador si pretenden admitir exploradores más antiguos.</span><span class="sxs-lookup"><span data-stu-id="832aa-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="832aa-194">ASP.NET Core no implementa la detección del explorador porque User-Agents valores son muy volátiles y cambian con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="832aa-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="832aa-195">Un punto de extensión de <xref:Microsoft.AspNetCore.CookiePolicy> permite conectar User-Agent lógica específica.</span><span class="sxs-lookup"><span data-stu-id="832aa-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="832aa-196">En `Startup.Configure` , agregue código que llame a <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> antes <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> de llamar a o a *cualquier* método que escriba cookie s:</span><span class="sxs-lookup"><span data-stu-id="832aa-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="832aa-197">En `Startup.ConfigureServices` , agregue código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="832aa-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="832aa-198">En el ejemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` es una biblioteca suministrada por el usuario que detecta si el agente de usuario no es compatible con SameSite `None` :</span><span class="sxs-lookup"><span data-stu-id="832aa-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="832aa-199">En el código siguiente se muestra un método de ejemplo `DisallowsSameSiteNone` :</span><span class="sxs-lookup"><span data-stu-id="832aa-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="832aa-200">El código siguiente es solo para la demostración:</span><span class="sxs-lookup"><span data-stu-id="832aa-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="832aa-201">No debe considerarse como completa.</span><span class="sxs-lookup"><span data-stu-id="832aa-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="832aa-202">No se mantiene ni se admite.</span><span class="sxs-lookup"><span data-stu-id="832aa-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="832aa-203">Probar aplicaciones para problemas de SameSite</span><span class="sxs-lookup"><span data-stu-id="832aa-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="832aa-204">Las aplicaciones que interactúan con sitios remotos, como a través de un inicio de sesión de terceros, deben:</span><span class="sxs-lookup"><span data-stu-id="832aa-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="832aa-205">Pruebe la interacción en varios exploradores.</span><span class="sxs-lookup"><span data-stu-id="832aa-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="832aa-206">Aplique la [ Cookie detección y la mitigación del explorador de directivas](#sob) que se describen en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="832aa-207">Pruebe las aplicaciones web con una versión de cliente que pueda participar en el nuevo comportamiento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="832aa-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="832aa-208">Chrome, Firefox y el borde de cromo tienen nuevas marcas de características opcionales que se pueden usar para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="832aa-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="832aa-209">Una vez que la aplicación aplica las revisiones de SameSite, probarla con versiones de cliente anteriores, especialmente Safari.</span><span class="sxs-lookup"><span data-stu-id="832aa-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="832aa-210">Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="832aa-211">Prueba con Chrome</span><span class="sxs-lookup"><span data-stu-id="832aa-211">Test with Chrome</span></span>

<span data-ttu-id="832aa-212">Chrome 78 + ofrece resultados engañosos porque tiene una mitigación temporal en contexto.</span><span class="sxs-lookup"><span data-stu-id="832aa-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="832aa-213">La mitigación temporal de Chrome 78 + permite cookie menos de dos minutos de antigüedad.</span><span class="sxs-lookup"><span data-stu-id="832aa-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="832aa-214">Chrome 76 o 77 con las marcas de prueba adecuadas habilitadas proporcionan resultados más precisos.</span><span class="sxs-lookup"><span data-stu-id="832aa-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="832aa-215">Para probar el nuevo comportamiento de SameSite, cambie `chrome://flags/#same-site-by-default-cookies` a **habilitado** .</span><span class="sxs-lookup"><span data-stu-id="832aa-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled** .</span></span> <span data-ttu-id="832aa-216">Las versiones anteriores de Chrome (75 e inferiores) se muestran como erróneas con la nueva `None` configuración.</span><span class="sxs-lookup"><span data-stu-id="832aa-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="832aa-217">Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="832aa-218">Google no hace que las versiones anteriores de Chrome estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="832aa-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="832aa-219">Siga las instrucciones de [Descargar cromo](https://www.chromium.org/getting-involved/download-chromium) para probar versiones anteriores de Chrome.</span><span class="sxs-lookup"><span data-stu-id="832aa-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="832aa-220">**No** Descargue Chrome desde los vínculos que se proporcionan al buscar versiones anteriores de Chrome.</span><span class="sxs-lookup"><span data-stu-id="832aa-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="832aa-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="832aa-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="832aa-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="832aa-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="832aa-223">A partir de la versión Canarias `80.0.3975.0` , se puede deshabilitar la mitigación temporal más estricta y posterior a la hora de realizar pruebas con la nueva marca `--enable-features=SameSiteDefaultChecksMethodRigorously` para permitir la realización de pruebas de sitios y servicios en el estado final eventual de la característica en la que se ha quitado la mitigación.</span><span class="sxs-lookup"><span data-stu-id="832aa-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="832aa-224">Para obtener más información, consulte las actualizaciones de los proyectos de cromo [SameSite](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="832aa-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="832aa-225">Prueba con Safari</span><span class="sxs-lookup"><span data-stu-id="832aa-225">Test with Safari</span></span>

<span data-ttu-id="832aa-226">Safari 12 implementó estrictamente el borrador anterior y produce un error cuando el nuevo `None` valor está en cookie .</span><span class="sxs-lookup"><span data-stu-id="832aa-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="832aa-227">`None` se evita a través del código de detección del explorador que [admite exploradores más antiguos](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="832aa-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="832aa-228">Pruebe los inicios de sesión de estilo de SO basados en Safari 12, Safari 13 y WebKit con MSAL, ADAL o cualquier biblioteca que use.</span><span class="sxs-lookup"><span data-stu-id="832aa-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="832aa-229">El problema depende de la versión del sistema operativo subyacente.</span><span class="sxs-lookup"><span data-stu-id="832aa-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="832aa-230">OSX Mojave (10,14) e iOS 12 se sabe que tienen problemas de compatibilidad con el nuevo comportamiento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="832aa-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="832aa-231">Al actualizar el sistema operativo a OSX Catalina (10,15) o iOS 13 se corrige el problema.</span><span class="sxs-lookup"><span data-stu-id="832aa-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="832aa-232">Safari no tiene actualmente una marca de participación para probar el nuevo comportamiento de la especificación.</span><span class="sxs-lookup"><span data-stu-id="832aa-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="832aa-233">Prueba con Firefox</span><span class="sxs-lookup"><span data-stu-id="832aa-233">Test with Firefox</span></span>

<span data-ttu-id="832aa-234">La compatibilidad con Firefox para el nuevo estándar se puede probar en la versión 68 + al optar por la `about:config` página con la marca de la característica `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="832aa-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="832aa-235">No ha habido informes de problemas de compatibilidad con versiones anteriores de Firefox.</span><span class="sxs-lookup"><span data-stu-id="832aa-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="832aa-236">Probar con el explorador Edge</span><span class="sxs-lookup"><span data-stu-id="832aa-236">Test with Edge browser</span></span>

<span data-ttu-id="832aa-237">Edge es compatible con el estándar SameSite antiguo.</span><span class="sxs-lookup"><span data-stu-id="832aa-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="832aa-238">La versión perimetral 44 no tiene ningún problema de compatibilidad conocido con el nuevo estándar.</span><span class="sxs-lookup"><span data-stu-id="832aa-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="832aa-239">Prueba con borde (cromo)</span><span class="sxs-lookup"><span data-stu-id="832aa-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="832aa-240">Las marcas SameSite se establecen en la `edge://flags/#same-site-by-default-cookies` página.</span><span class="sxs-lookup"><span data-stu-id="832aa-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="832aa-241">No se detectaron problemas de compatibilidad con el cromo perimetral.</span><span class="sxs-lookup"><span data-stu-id="832aa-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="832aa-242">Prueba con Electron</span><span class="sxs-lookup"><span data-stu-id="832aa-242">Test with Electron</span></span>

<span data-ttu-id="832aa-243">Las versiones de Electron incluyen versiones anteriores de cromo.</span><span class="sxs-lookup"><span data-stu-id="832aa-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="832aa-244">Por ejemplo, la versión de Electron utilizada por los equipos es cromo 66, que exhibe el comportamiento anterior.</span><span class="sxs-lookup"><span data-stu-id="832aa-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="832aa-245">Debe realizar sus propias pruebas de compatibilidad con la versión de que Electron usa el producto.</span><span class="sxs-lookup"><span data-stu-id="832aa-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="832aa-246">Consulte [compatibilidad con exploradores anteriores](#sob) en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="832aa-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="832aa-247">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="832aa-247">Additional resources</span></span>

* [<span data-ttu-id="832aa-248">Blog de cromo: desarrolladores: Prepárese para New SameSite = None; Cookie Configuración segura</span><span class="sxs-lookup"><span data-stu-id="832aa-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="832aa-249">[SameSite cookie s explicados](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="832aa-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="832aa-250">Revisiones de noviembre de 2019</span><span class="sxs-lookup"><span data-stu-id="832aa-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="832aa-251">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="832aa-251">Sample</span></span>               | <span data-ttu-id="832aa-252">Documento</span><span class="sxs-lookup"><span data-stu-id="832aa-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="832aa-253">MVC de .NET Core</span><span class="sxs-lookup"><span data-stu-id="832aa-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="832aa-254">[Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="832aa-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="832aa-255">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="832aa-255">Sample</span></span>               | <span data-ttu-id="832aa-256">Documento</span><span class="sxs-lookup"><span data-stu-id="832aa-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="832aa-257">[Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="832aa-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
