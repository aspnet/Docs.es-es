---
title: 'Usar cookie autenticación sin ASP.NET Core Identity'
author: rick-anderson
description: 'Aprenda a usar la cookie autenticación sin ASP.NET Core Identity .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/cookie'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061359"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="4bbab-103">Usar cookie autenticación sin ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="4bbab-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="4bbab-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bbab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bbab-105">ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4bbab-106">Sin embargo, cookie se puede usar un proveedor de autenticación basado en ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4bbab-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4bbab-107">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4bbab-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bbab-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4bbab-109">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4bbab-110">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4bbab-111">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4bbab-112">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4bbab-113">Configuración</span><span class="sxs-lookup"><span data-stu-id="4bbab-113">Configuration</span></span>

<span data-ttu-id="4bbab-114">En el `Startup.ConfigureServices` método, cree los servicios de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="4bbab-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4bbab-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4bbab-116">`AuthenticationScheme` resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4bbab-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4bbab-117">Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4bbab-118">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4bbab-119">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4bbab-120">Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="4bbab-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4bbab-121">La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4bbab-122">Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4bbab-123">Para obtener más información, vea <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4bbab-124">En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` para establecer la `HttpContext.User` propiedad y ejecutar el middleware de autorización para las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="4bbab-125">Llame a `UseAuthentication` los `UseAuthorization` métodos y antes de llamar a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4bbab-126">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4bbab-127">Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="4bbab-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4bbab-128">Cookie Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="4bbab-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="4bbab-129">El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4bbab-130">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="4bbab-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4bbab-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="4bbab-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4bbab-132">El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4bbab-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4bbab-133">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4bbab-134">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4bbab-135">La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4bbab-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4bbab-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4bbab-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-137">Cookie.SameSite</span></span> | <span data-ttu-id="4bbab-138">Resultado Cookie . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4bbab-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-139">SameSiteMode.None</span></span>     | <span data-ttu-id="4bbab-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-140">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-143">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4bbab-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-147">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4bbab-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-154">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4bbab-160">Crear una autenticación cookie</span><span class="sxs-lookup"><span data-stu-id="4bbab-160">Create an authentication cookie</span></span>

<span data-ttu-id="4bbab-161">Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4bbab-162">La información del usuario se serializa y se almacena en el cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4bbab-163">Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="4bbab-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="4bbab-164">`SignInAsync` crea un cifrado cookie y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4bbab-165">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> solo se usa en algunas rutas de acceso específicas de forma predeterminada, por ejemplo, las rutas de acceso de inicio de sesión y de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="4bbab-167">Para obtener más información, vea el [ Cookie origen de AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="4bbab-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="4bbab-168">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4bbab-169">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4bbab-170">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4bbab-170">Sign out</span></span>

<span data-ttu-id="4bbab-171">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4bbab-172">Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4bbab-173">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-174">Cuando el explorador se cierra automáticamente, se eliminan los elementos basados en sesión cookie (no persistentes cookie ), pero no cookie se borran los s cuando se cierra una pestaña individual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="4bbab-175">El servidor no recibe notificaciones de eventos de cierre de pestaña o de explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4bbab-176">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="4bbab-176">React to back-end changes</span></span>

<span data-ttu-id="4bbab-177">Una vez que cookie se crea un, cookie es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4bbab-178">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="4bbab-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4bbab-179">El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4bbab-180">El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.</span><span class="sxs-lookup"><span data-stu-id="4bbab-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4bbab-181">El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4bbab-182">La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4bbab-183">Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4bbab-184">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4bbab-185">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4bbab-186">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4bbab-187">Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="4bbab-188">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4bbab-189">El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4bbab-190">Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="4bbab-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4bbab-191">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="4bbab-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4bbab-192">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4bbab-193">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4bbab-194">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="4bbab-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4bbab-195">La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4bbab-196">cookiePersistentes</span><span class="sxs-lookup"><span data-stu-id="4bbab-196">Persistent cookies</span></span>

<span data-ttu-id="4bbab-197">Puede que desee que el se cookie conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4bbab-198">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4bbab-199">El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4bbab-200">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4bbab-201">Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="4bbab-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4bbab-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4bbab-203">cookieExpiración absoluta</span><span class="sxs-lookup"><span data-stu-id="4bbab-203">Absolute cookie expiration</span></span>

<span data-ttu-id="4bbab-204">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4bbab-205">Para crear un persistente cookie , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="4bbab-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4bbab-206">De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="4bbab-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4bbab-207">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="4bbab-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4bbab-208">El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4bbab-209">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bbab-210">ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4bbab-211">Sin embargo, cookie se puede usar un proveedor de autenticación basado en ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4bbab-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4bbab-212">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4bbab-213">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bbab-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4bbab-214">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4bbab-215">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4bbab-216">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4bbab-217">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4bbab-218">Configuración</span><span class="sxs-lookup"><span data-stu-id="4bbab-218">Configuration</span></span>

<span data-ttu-id="4bbab-219">Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. Cookie paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="4bbab-220">En el `Startup.ConfigureServices` método, cree el servicio de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="4bbab-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4bbab-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4bbab-222">`AuthenticationScheme` resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4bbab-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4bbab-223">Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4bbab-224">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4bbab-225">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4bbab-226">Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="4bbab-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4bbab-227">La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4bbab-228">Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4bbab-229">Para obtener más información, vea <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4bbab-230">En el `Startup.Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4bbab-231">Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4bbab-232">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4bbab-233">Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="4bbab-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4bbab-234">Cookie Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="4bbab-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="4bbab-235">El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4bbab-236">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="4bbab-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4bbab-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="4bbab-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4bbab-238">El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4bbab-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4bbab-239">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4bbab-240">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4bbab-241">La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4bbab-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4bbab-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4bbab-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-243">Cookie.SameSite</span></span> | <span data-ttu-id="4bbab-244">Resultado Cookie . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4bbab-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-245">SameSiteMode.None</span></span>     | <span data-ttu-id="4bbab-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-246">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-249">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4bbab-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-253">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4bbab-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-260">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4bbab-266">Crear una autenticación cookie</span><span class="sxs-lookup"><span data-stu-id="4bbab-266">Create an authentication cookie</span></span>

<span data-ttu-id="4bbab-267">Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4bbab-268">La información del usuario se serializa y se almacena en el cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4bbab-269">Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="4bbab-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4bbab-270">`SignInAsync` crea un cifrado cookie y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4bbab-271">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-272">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4bbab-273">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4bbab-274">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4bbab-274">Sign out</span></span>

<span data-ttu-id="4bbab-275">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4bbab-276">Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4bbab-277">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4bbab-278">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="4bbab-278">React to back-end changes</span></span>

<span data-ttu-id="4bbab-279">Una vez que cookie se crea un, cookie es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4bbab-280">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="4bbab-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4bbab-281">El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="4bbab-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4bbab-282">El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.</span><span class="sxs-lookup"><span data-stu-id="4bbab-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4bbab-283">El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4bbab-284">La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4bbab-285">Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4bbab-286">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4bbab-287">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4bbab-288">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4bbab-289">Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="4bbab-290">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4bbab-291">El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4bbab-292">Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="4bbab-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4bbab-293">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="4bbab-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4bbab-294">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4bbab-295">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4bbab-296">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="4bbab-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4bbab-297">La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4bbab-298">cookiePersistentes</span><span class="sxs-lookup"><span data-stu-id="4bbab-298">Persistent cookies</span></span>

<span data-ttu-id="4bbab-299">Puede que desee que el se cookie conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4bbab-300">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4bbab-301">El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4bbab-302">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4bbab-303">Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="4bbab-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4bbab-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4bbab-305">cookieExpiración absoluta</span><span class="sxs-lookup"><span data-stu-id="4bbab-305">Absolute cookie expiration</span></span>

<span data-ttu-id="4bbab-306">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4bbab-307">Para crear un persistente cookie , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="4bbab-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4bbab-308">De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="4bbab-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4bbab-309">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="4bbab-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4bbab-310">El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4bbab-311">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4bbab-312">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4bbab-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="4bbab-313">Comprobaciones de roles basadas en directivas</span><span class="sxs-lookup"><span data-stu-id="4bbab-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
