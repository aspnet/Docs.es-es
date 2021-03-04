---
title: Trabajar con SameSite cookie s en ASP.net Core
author: rick-anderson
description: Aprenda a usar SameSite cookie s en ASP.net Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- appsettings.json
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
- Electron
uid: security/samesite
ms.openlocfilehash: e06c73edfc999053e0aa37f05d984a2b428f69a9
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109941"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>Trabajar con SameSite cookie s en ASP.net Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite es un estándar de borrador de [IETF](https://ietf.org/about/) diseñado para proporcionar protección contra los ataques de falsificación de solicitud entre sitios (CSRF). Originalmente elaborado en [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), el borrador estándar se actualizó en [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00). El estándar actualizado no es compatible con versiones anteriores del estándar anterior, y las diferencias más destacables son las siguientes:

* Cookies sin el encabezado SameSite se tratan como `SameSite=Lax` de forma predeterminada.
* `SameSite=None` debe usarse para permitir el uso entre sitios cookie .
* Cookies que la aserción `SameSite=None` también debe marcarse como `Secure` .
* Las aplicaciones que usan [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) pueden experimentar problemas con `sameSite=Lax` o `sameSite=Strict` cookie , ya que `<iframe>` se tratan como escenarios entre sitios.
* El `SameSite=None` [estándar 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) no permite el valor y hace que algunas implementaciones traten tales cookie como `SameSite=Strict` . Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

La `SameSite=Lax` configuración funciona con la mayoría de las aplicaciones cookie . Algunas formas de autenticación como [OpenID Connect](https://openid.net/connect/) (OIDC) y [WS-Federation](https://auth0.com/docs/protocols/ws-fed) tienen como valor predeterminado el envío de redirecciones basadas en post. Las redirecciones basadas en POST desencadenan las protecciones del explorador de SameSite, por lo que SameSite está deshabilitado para estos componentes. La mayoría de los inicios de sesión de [OAuth](https://oauth.net/) no se ven afectados debido a las diferencias en el modo en que fluye la solicitud.

Cada componente de ASP.NET Core que emite cookie s necesita decidir si SameSite es adecuado.

## <a name="samesite-and-identity"></a>SameSite y Identity

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a>Código de ejemplo de prueba de SameSite

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Los ejemplos siguientes se pueden descargar y probar:

| Muestra               | Documento |
| ----------------- | ------------ |
| [MVC de .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

El ejemplo siguiente se puede descargar y probar:

| Muestra               | Documento |
| ----------------- | ------------ |
| [Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>Compatibilidad de .NET Core con el atributo sameSite

.NET Core 2,2 y versiones posteriores admiten el borrador estándar 2019 de SameSite desde la publicación de actualizaciones en diciembre de 2019. Los desarrolladores pueden controlar mediante programación el valor del atributo sameSite mediante la `HttpCookie.SameSite` propiedad. Si se establece la `SameSite` propiedad en Strict, LAX o None, los valores se escriben en la red con cookie . Si se establece en, `(SameSiteMode)(-1)` indica que no se debe incluir ningún atributo sameSite en la red con el parámetro cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.NET Core 3,0 y versiones posteriores admiten los valores de SameSite actualizados y agrega un valor de enumeración adicional `SameSiteMode.Unspecified` a la `SameSiteMode` enumeración.
Este nuevo valor indica que no se debe enviar ningún sameSite con cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>Cambios de comportamiento de revisión de diciembre

El cambio de comportamiento específico para .NET Framework y .NET Core 2,1 es el modo en que la `SameSite` propiedad interpreta el `None` valor. Antes de que la revisión sea un valor de `None` pensado "no emitir el atributo en absoluto", después de la revisión significa "emitir el atributo con un valor de `None` ". Después de que la revisión sea un `SameSite` valor de `(SameSiteMode)(-1)` , no se emitirá el atributo.

El valor predeterminado de SameSite para la autenticación de formularios y el estado cookie de sesión s se cambió de `None` a `Lax` .

::: moniker-end

## <a name="api-usage-with-samesite"></a>Uso de la API con SameSite

[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) tiene como valor predeterminado `Unspecified` , lo que significa que no se ha agregado ningún atributo SameSite a cookie y el cliente usará su comportamiento predeterminado (LAX para los nuevos exploradores, ninguno para los antiguos). En el código siguiente se muestra cómo cambiar el cookie valor de SameSite a `SameSiteMode.Lax` :

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Todos los componentes de ASP.NET Core que emiten cookie s invalidan los valores predeterminados anteriores con la configuración adecuada para sus escenarios. Los valores predeterminados anteriores invalidados no han cambiado.

| Componente | cookie | Default |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie Autenticación](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3,1 y versiones posteriores proporcionan la siguiente compatibilidad con SameSite:

* Vuelve a definir el comportamiento de `SameSiteMode.None` para emitir. `SameSite=None`
* Agrega un nuevo valor `SameSiteMode.Unspecified` para omitir el atributo SameSite.
* Todas las cookie API de tienen como valor predeterminado `Unspecified` . Algunos componentes que utilizan cookie s establecen valores más específicos de sus escenarios. Vea la tabla anterior para obtener ejemplos.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

En ASP.NET Core 3,0 y versiones posteriores, se cambiaron los valores predeterminados de SameSite para evitar conflictos con los valores predeterminados de cliente incoherentes. Las siguientes API han cambiado el valor predeterminado de `SameSiteMode.Lax ` a `-1` para evitar la emisión de un atributo SameSite para estos cookie s:

* <xref:Microsoft.AspNetCore.Http.CookieOptions> se usa con [HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>  se usa como generador para `CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Historial y cambios

La compatibilidad con SameSite se implementó por primera vez en ASP.NET Core en 2,0 con el [borrador estándar de 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). El estándar 2016 era participación. ASP.NET Core participar estableciendo varias cookie s en de `Lax` forma predeterminada. Después de encontrar varios [problemas](https://github.com/aspnet/Announcements/issues/318) con la autenticación, se [deshabilitó](https://github.com/aspnet/Announcements/issues/348)la mayor parte del uso de SameSite.

Las [revisiones](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) se emitieron en noviembre de 2019 para actualizar desde el estándar 2016 al estándar 2019. El [borrador 2019 de la especificación SameSite](https://github.com/aspnet/Announcements/issues/390):

* **No** es compatible con las versiones anteriores del borrador 2016. Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.
* Especifica cookie que se tratan como `SameSite=Lax` de forma predeterminada.
* Especifica cookie s que la aserción explícitamente `SameSite=None` para habilitar la entrega entre sitios debe marcarse como `Secure` . `None` es una nueva entrada que se va a dejar de participar.
* Es compatible con las revisiones emitidas para ASP.NET Core 2,1, 2,2 y 3,0. ASP.NET Core 3,1 tiene compatibilidad adicional con SameSite.
* Está programado para que [Chrome](https://chromestatus.com/feature/5088147346030592) lo habilite de forma predeterminada en [febrero de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html). Los exploradores empezaron a pasar a este estándar en 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>API afectadas por el cambio del estándar de borrador de 2016 SameSite al borrador estándar de 2019

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieGenerador. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Compatibilidad con exploradores más antiguos

El estándar 2016 SameSite impediba que los valores desconocidos se deben tratar como `SameSite=Strict` valores. Las aplicaciones a las que se accede desde exploradores más antiguos que admiten el estándar 2016 SameSite se pueden interrumpir cuando obtienen una propiedad SameSite con un valor de `None` . Las aplicaciones web deben implementar la detección del explorador si pretenden admitir exploradores más antiguos. ASP.NET Core no implementa la detección del explorador porque User-Agents valores son muy volátiles y cambian con frecuencia. Un punto de extensión de <xref:Microsoft.AspNetCore.CookiePolicy> permite conectar User-Agent lógica específica.

En `Startup.Configure` , agregue código que llame a <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> antes <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> de llamar a o a *cualquier* método que escriba cookie s:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

En `Startup.ConfigureServices` , agregue código similar al siguiente:

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

En el ejemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` es una biblioteca suministrada por el usuario que detecta si el agente de usuario no es compatible con SameSite `None` :

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

En el código siguiente se muestra un método de ejemplo `DisallowsSameSiteNone` :

> [!WARNING]
> El código siguiente es solo para la demostración:
> * No debe considerarse como completa.
> * No se mantiene ni se admite.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Probar aplicaciones para problemas de SameSite

Las aplicaciones que interactúan con sitios remotos, como a través de un inicio de sesión de terceros, deben:

* Pruebe la interacción en varios exploradores.
* Aplique la [ Cookie detección y la mitigación del explorador de directivas](#sob) que se describen en este documento.

Pruebe las aplicaciones web con una versión de cliente que pueda participar en el nuevo comportamiento de SameSite. Chrome, Firefox y el borde de cromo tienen nuevas marcas de características opcionales que se pueden usar para las pruebas. Una vez que la aplicación aplica las revisiones de SameSite, probarla con versiones de cliente anteriores, especialmente Safari. Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

### <a name="test-with-chrome"></a>Prueba con Chrome

Chrome 78 + ofrece resultados engañosos porque tiene una mitigación temporal en contexto. La mitigación temporal de Chrome 78 + permite cookie menos de dos minutos de antigüedad. Chrome 76 o 77 con las marcas de prueba adecuadas habilitadas proporcionan resultados más precisos. Para probar el nuevo comportamiento de SameSite, cambie `chrome://flags/#same-site-by-default-cookies` a **habilitado**. Las versiones anteriores de Chrome (75 e inferiores) se muestran como erróneas con la nueva `None` configuración. Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.

Google no hace que las versiones anteriores de Chrome estén disponibles. Siga las instrucciones de [Descargar cromo](https://www.chromium.org/getting-involved/download-chromium) para probar versiones anteriores de Chrome. **No** Descargue Chrome desde los vínculos que se proporcionan al buscar versiones anteriores de Chrome.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

A partir de la versión Canarias `80.0.3975.0` , se puede deshabilitar la mitigación temporal más estricta y posterior a la hora de realizar pruebas con la nueva marca `--enable-features=SameSiteDefaultChecksMethodRigorously` para permitir la realización de pruebas de sitios y servicios en el estado final eventual de la característica en la que se ha quitado la mitigación. Para obtener más información, consulte las actualizaciones de los proyectos de cromo [SameSite](https://www.chromium.org/updates/same-site)

### <a name="test-with-safari"></a>Prueba con Safari

Safari 12 implementó estrictamente el borrador anterior y produce un error cuando el nuevo `None` valor está en cookie . `None` se evita a través del código de detección del explorador que [admite exploradores más antiguos](#sob) en este documento. Pruebe los inicios de sesión de estilo de SO basados en Safari 12, Safari 13 y WebKit con MSAL, ADAL o cualquier biblioteca que use. El problema depende de la versión del sistema operativo subyacente. OSX Mojave (10,14) e iOS 12 se sabe que tienen problemas de compatibilidad con el nuevo comportamiento de SameSite. Al actualizar el sistema operativo a OSX Catalina (10,15) o iOS 13 se corrige el problema. Safari no tiene actualmente una marca de participación para probar el nuevo comportamiento de la especificación.

### <a name="test-with-firefox"></a>Prueba con Firefox

La compatibilidad con Firefox para el nuevo estándar se puede probar en la versión 68 + al optar por la `about:config` página con la marca de la característica `network.cookie.sameSite.laxByDefault` . No ha habido informes de problemas de compatibilidad con versiones anteriores de Firefox.

### <a name="test-with-edge-browser"></a>Probar con el explorador Edge

Edge es compatible con el estándar SameSite antiguo. La versión perimetral 44 no tiene ningún problema de compatibilidad conocido con el nuevo estándar.

### <a name="test-with-edge-chromium"></a>Prueba con borde (cromo)

Las marcas SameSite se establecen en la `edge://flags/#same-site-by-default-cookies` página. No se detectaron problemas de compatibilidad con el cromo perimetral.

### <a name="test-with-electron"></a>Prueba con Electron

Las versiones de Electron incluyen versiones anteriores de cromo. Por ejemplo, la versión de Electron utilizada por los equipos es cromo 66, que exhibe el comportamiento anterior. Debe realizar sus propias pruebas de compatibilidad con la versión de que Electron usa el producto. Consulte [compatibilidad con exploradores anteriores](#sob) en la sección siguiente.

## <a name="additional-resources"></a>Recursos adicionales

* [Blog de cromo: desarrolladores: Prepárese para New SameSite = None; Cookie Configuración segura](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookie s explicados](https://web.dev/samesite-cookies-explained/)
* [Revisiones de noviembre de 2019](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| Muestra               | Documento |
| ----------------- | ------------ |
| [MVC de .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| Muestra               | Documento |
| ----------------- | ------------ |
| [Páginas de .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
