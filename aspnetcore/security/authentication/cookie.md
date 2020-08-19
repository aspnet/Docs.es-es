---
title: Usar cookie autenticación sin ASP.NET Core Identity
author: rick-anderson
description: Aprenda a usar la cookie autenticación sin ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 2e9eb58837d74343d8de6903372146570b43f330
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627148"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="eaa8c-103">Usar cookie autenticación sin ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="eaa8c-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="eaa8c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eaa8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa8c-105">ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="eaa8c-106">Sin embargo, cookie se puede usar un proveedor de autenticación basado en ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="eaa8c-107">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="eaa8c-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa8c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eaa8c-109">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="eaa8c-110">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="eaa8c-111">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="eaa8c-112">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="eaa8c-113">Configuración</span><span class="sxs-lookup"><span data-stu-id="eaa8c-113">Configuration</span></span>

<span data-ttu-id="eaa8c-114">Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. Cookie paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="eaa8c-115">En el `Startup.ConfigureServices` método, cree los servicios de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="eaa8c-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="eaa8c-117">`AuthenticationScheme` resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="eaa8c-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="eaa8c-118">Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="eaa8c-119">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="eaa8c-120">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="eaa8c-121">Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="eaa8c-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="eaa8c-122">La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="eaa8c-123">Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="eaa8c-124">Para más información, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="eaa8c-125">En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` para establecer la `HttpContext.User` propiedad y ejecutar el middleware de autorización para las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="eaa8c-126">Llame a `UseAuthentication` los `UseAuthorization` métodos y antes de llamar a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="eaa8c-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="eaa8c-128">Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="eaa8c-129">Cookie Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="eaa8c-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="eaa8c-130">El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="eaa8c-131">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="eaa8c-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="eaa8c-133">El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="eaa8c-134">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="eaa8c-135">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="eaa8c-136">La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="eaa8c-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="eaa8c-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="eaa8c-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="eaa8c-138">Cookie.SameSite</span></span> | <span data-ttu-id="eaa8c-139">Resultado Cookie . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="eaa8c-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="eaa8c-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-140">SameSiteMode.None</span></span>     | <span data-ttu-id="eaa8c-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-141">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-144">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="eaa8c-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="eaa8c-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-148">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="eaa8c-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="eaa8c-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-155">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="eaa8c-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="eaa8c-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="eaa8c-161">Crear una autenticación cookie</span><span class="sxs-lookup"><span data-stu-id="eaa8c-161">Create an authentication cookie</span></span>

<span data-ttu-id="eaa8c-162">Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="eaa8c-163">La información del usuario se serializa y se almacena en el cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="eaa8c-164">Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="eaa8c-165">`SignInAsync` crea un cifrado cookie y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="eaa8c-166">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="eaa8c-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> solo se usa en algunas rutas de acceso específicas de forma predeterminada, por ejemplo, las rutas de acceso de inicio de sesión y de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="eaa8c-168">Para obtener más información, vea el [ Cookie origen de AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="eaa8c-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="eaa8c-169">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="eaa8c-170">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="eaa8c-171">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="eaa8c-171">Sign out</span></span>

<span data-ttu-id="eaa8c-172">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="eaa8c-173">Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="eaa8c-174">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="eaa8c-175">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="eaa8c-175">React to back-end changes</span></span>

<span data-ttu-id="eaa8c-176">Una vez que cookie se crea un, cookie es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="eaa8c-177">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="eaa8c-178">El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="eaa8c-179">El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="eaa8c-180">El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="eaa8c-181">La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="eaa8c-182">Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="eaa8c-183">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="eaa8c-184">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="eaa8c-185">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="eaa8c-186">Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="eaa8c-187">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="eaa8c-188">El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="eaa8c-189">Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eaa8c-190">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="eaa8c-191">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="eaa8c-192">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="eaa8c-193">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="eaa8c-194">La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="eaa8c-195">cookiePersistentes</span><span class="sxs-lookup"><span data-stu-id="eaa8c-195">Persistent cookies</span></span>

<span data-ttu-id="eaa8c-196">Puede que desee que el se cookie conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="eaa8c-197">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="eaa8c-198">El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="eaa8c-199">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="eaa8c-200">Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="eaa8c-201"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="eaa8c-202">cookieExpiración absoluta</span><span class="sxs-lookup"><span data-stu-id="eaa8c-202">Absolute cookie expiration</span></span>

<span data-ttu-id="eaa8c-203">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="eaa8c-204">Para crear un persistente cookie , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="eaa8c-205">De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="eaa8c-206">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="eaa8c-207">El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="eaa8c-208">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="eaa8c-209">ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="eaa8c-210">Sin embargo, cookie se puede usar un proveedor de autenticación de autenticación basado en ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="eaa8c-211">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="eaa8c-212">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa8c-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eaa8c-213">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="eaa8c-214">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="eaa8c-215">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="eaa8c-216">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="eaa8c-217">Configuración</span><span class="sxs-lookup"><span data-stu-id="eaa8c-217">Configuration</span></span>

<span data-ttu-id="eaa8c-218">Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. Cookie paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="eaa8c-219">En el `Startup.ConfigureServices` método, cree el servicio de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="eaa8c-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="eaa8c-221">`AuthenticationScheme` resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="eaa8c-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="eaa8c-222">Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="eaa8c-223">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="eaa8c-224">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="eaa8c-225">Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="eaa8c-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="eaa8c-226">La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="eaa8c-227">Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="eaa8c-228">Para más información, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="eaa8c-229">En el `Startup.Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="eaa8c-230">Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="eaa8c-231">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="eaa8c-232">Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="eaa8c-233">Cookie Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="eaa8c-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="eaa8c-234">El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="eaa8c-235">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="eaa8c-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="eaa8c-237">El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="eaa8c-238">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="eaa8c-239">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="eaa8c-240">La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="eaa8c-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="eaa8c-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="eaa8c-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="eaa8c-242">Cookie.SameSite</span></span> | <span data-ttu-id="eaa8c-243">Resultado Cookie . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="eaa8c-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="eaa8c-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-244">SameSiteMode.None</span></span>     | <span data-ttu-id="eaa8c-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-245">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-248">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="eaa8c-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="eaa8c-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-252">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="eaa8c-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="eaa8c-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="eaa8c-259">SameSiteMode.None</span></span><br><span data-ttu-id="eaa8c-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="eaa8c-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="eaa8c-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="eaa8c-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="eaa8c-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="eaa8c-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="eaa8c-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="eaa8c-265">Crear una autenticación cookie</span><span class="sxs-lookup"><span data-stu-id="eaa8c-265">Create an authentication cookie</span></span>

<span data-ttu-id="eaa8c-266">Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="eaa8c-267">La información del usuario se serializa y se almacena en el cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="eaa8c-268">Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="eaa8c-269">`SignInAsync` crea un cifrado cookie y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="eaa8c-270">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="eaa8c-271">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="eaa8c-272">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="eaa8c-273">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="eaa8c-273">Sign out</span></span>

<span data-ttu-id="eaa8c-274">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="eaa8c-275">Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="eaa8c-276">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="eaa8c-277">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="eaa8c-277">React to back-end changes</span></span>

<span data-ttu-id="eaa8c-278">Una vez que cookie se crea un, cookie es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="eaa8c-279">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="eaa8c-280">El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="eaa8c-281">El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="eaa8c-282">El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="eaa8c-283">La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="eaa8c-284">Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="eaa8c-285">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="eaa8c-286">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="eaa8c-287">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="eaa8c-288">Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="eaa8c-289">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="eaa8c-290">El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="eaa8c-291">Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eaa8c-292">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="eaa8c-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="eaa8c-293">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="eaa8c-294">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="eaa8c-295">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="eaa8c-296">La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="eaa8c-297">cookiePersistentes</span><span class="sxs-lookup"><span data-stu-id="eaa8c-297">Persistent cookies</span></span>

<span data-ttu-id="eaa8c-298">Puede que desee que el se cookie conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="eaa8c-299">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="eaa8c-300">El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="eaa8c-301">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="eaa8c-302">Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="eaa8c-303"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="eaa8c-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="eaa8c-304">cookieExpiración absoluta</span><span class="sxs-lookup"><span data-stu-id="eaa8c-304">Absolute cookie expiration</span></span>

<span data-ttu-id="eaa8c-305">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="eaa8c-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="eaa8c-306">Para crear un persistente cookie , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="eaa8c-307">De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="eaa8c-308">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="eaa8c-309">El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="eaa8c-310">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="eaa8c-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="eaa8c-311">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="eaa8c-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="eaa8c-312">Comprobaciones de roles basadas en directivas</span><span class="sxs-lookup"><span data-stu-id="eaa8c-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
