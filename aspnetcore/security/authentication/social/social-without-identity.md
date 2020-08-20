---
title: La autenticación de Facebook, Google y proveedores externos sin ASP.NET Core Identity
author: rick-anderson
description: Una explicación del uso de Facebook, Google, Twitter, etc. autenticación de usuarios de cuentas sin ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: a91a2f2fb7873e5a672c624e9cf863ae720c8005
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634233"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="4ec8f-103">Usar autenticación de proveedor de inicio de sesión social sin ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="4ec8f-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="4ec8f-104">Por [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ec8f-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ec8f-105"><xref:security/authentication/social/index> describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="4ec8f-106">El enfoque descrito en dicho tema incluye ASP.NET Core Identity como proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="4ec8f-107">Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="4ec8f-108">Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core Identity , pero que aún requieren la integración con un proveedor de autenticación externo de confianza.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="4ec8f-109">Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="4ec8f-110">El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="4ec8f-111">Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="4ec8f-112">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="4ec8f-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="4ec8f-113">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="4ec8f-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="4ec8f-114">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="4ec8f-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="4ec8f-115">Otros proveedores</span><span class="sxs-lookup"><span data-stu-id="4ec8f-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="4ec8f-116">Configuración</span><span class="sxs-lookup"><span data-stu-id="4ec8f-116">Configuration</span></span>

<span data-ttu-id="4ec8f-117">En el `ConfigureServices` método, configure los esquemas de autenticación de la aplicación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos, y <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="4ec8f-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="4ec8f-118">La llamada a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> establece el de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="4ec8f-119">`DefaultScheme`Es el esquema predeterminado que usan los siguientes `HttpContext` métodos de extensión de autenticación:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="4ec8f-120">Al establecer el valor de la aplicación `DefaultScheme` en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), se configura la aplicación para que use Cookie s como esquema predeterminado para estos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="4ec8f-121">Al establecer el valor de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="4ec8f-122">`DefaultChallengeScheme` invalida `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="4ec8f-123">Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para ver las propiedades adicionales que invalidan `DefaultScheme` cuando se establece.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="4ec8f-124">En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` entre las llamadas a `UseRouting` y `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="4ec8f-125">Esto establece la `HttpContext.User` propiedad y ejecuta el middleware de autorización para las solicitudes:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="4ec8f-126">Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="4ec8f-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="4ec8f-127">Para obtener más información acerca de la cookie autenticación, vea <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="4ec8f-128">Aplicar autorización</span><span class="sxs-lookup"><span data-stu-id="4ec8f-128">Apply authorization</span></span>

<span data-ttu-id="4ec8f-129">Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, una acción o una página.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="4ec8f-130">El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="4ec8f-131">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4ec8f-131">Sign out</span></span>

<span data-ttu-id="4ec8f-132">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="4ec8f-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="4ec8f-133">En el código siguiente se agrega un `Logout` controlador de página a la página de *Índice* :</span><span class="sxs-lookup"><span data-stu-id="4ec8f-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="4ec8f-134">Observe que la llamada a no `SignOutAsync` especifica un esquema de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="4ec8f-135">La de la aplicación `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ec8f-136">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4ec8f-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ec8f-137"><xref:security/authentication/social/index> describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="4ec8f-138">El enfoque descrito en dicho tema incluye ASP.NET Core Identity como proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="4ec8f-139">Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="4ec8f-140">Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core Identity , pero que aún requieren la integración con un proveedor de autenticación externo de confianza.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="4ec8f-141">Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="4ec8f-142">El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="4ec8f-143">Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="4ec8f-144">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="4ec8f-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="4ec8f-145">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="4ec8f-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="4ec8f-146">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="4ec8f-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="4ec8f-147">Otros proveedores</span><span class="sxs-lookup"><span data-stu-id="4ec8f-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="4ec8f-148">Configuración</span><span class="sxs-lookup"><span data-stu-id="4ec8f-148">Configuration</span></span>

<span data-ttu-id="4ec8f-149">En el `ConfigureServices` método, configure los esquemas de autenticación de la aplicación con los `AddAuthentication` `AddCookie` métodos, y `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="4ec8f-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="4ec8f-150">La llamada a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) establece el [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="4ec8f-151">`DefaultScheme`Es el esquema predeterminado que usan los siguientes `HttpContext` métodos de extensión de autenticación:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="4ec8f-152">Al establecer el valor de la aplicación `DefaultScheme` en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), se configura la aplicación para que use Cookie s como esquema predeterminado para estos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="4ec8f-153">Al establecer el valor de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="4ec8f-154">`DefaultChallengeScheme` invalida `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="4ec8f-155">Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para ver las propiedades adicionales que invalidan `DefaultScheme` cuando se establece.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="4ec8f-156">En el `Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4ec8f-157">Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="4ec8f-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="4ec8f-158">Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="4ec8f-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="4ec8f-159">Para obtener más información acerca de la cookie autenticación, vea <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="4ec8f-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="4ec8f-160">Aplicar autorización</span><span class="sxs-lookup"><span data-stu-id="4ec8f-160">Apply authorization</span></span>

<span data-ttu-id="4ec8f-161">Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, una acción o una página.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="4ec8f-162">El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:</span><span class="sxs-lookup"><span data-stu-id="4ec8f-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="4ec8f-163">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4ec8f-163">Sign out</span></span>

<span data-ttu-id="4ec8f-164">Para cerrar la sesión del usuario actual y eliminar su cookie , llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="4ec8f-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="4ec8f-165">En el código siguiente se agrega un `Logout` controlador de página a la página de *Índice* :</span><span class="sxs-lookup"><span data-stu-id="4ec8f-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="4ec8f-166">Observe que la llamada a no `SignOutAsync` especifica un esquema de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="4ec8f-167">La de la aplicación `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.</span><span class="sxs-lookup"><span data-stu-id="4ec8f-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ec8f-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4ec8f-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
