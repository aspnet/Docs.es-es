---
title: 'Usar :::no-loc(cookie)::: autenticación sin :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Aprenda a usar la :::no-loc(cookie)::: autenticación sin :::no-loc(ASP.NET Core Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061359"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="4bbab-103">Usar :::no-loc(cookie)::: autenticación sin :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="4bbab-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="4bbab-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bbab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bbab-105">:::no-loc(ASP.NET Core Identity)::: es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4bbab-106">Sin embargo, :::no-loc(cookie)::: se puede usar un proveedor de autenticación basado en :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="4bbab-107">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4bbab-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bbab-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4bbab-109">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4bbab-110">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4bbab-111">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4bbab-112">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4bbab-113">Configuración</span><span class="sxs-lookup"><span data-stu-id="4bbab-113">Configuration</span></span>

<span data-ttu-id="4bbab-114">En el `Startup.ConfigureServices` método, cree los servicios de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="4bbab-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="4bbab-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4bbab-116">`AuthenticationScheme` resulta útil cuando hay varias instancias de :::no-loc(cookie)::: autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4bbab-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4bbab-117">Si `AuthenticationScheme` se establece en [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " :::no-loc(Cookie)::: s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="4bbab-118">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4bbab-119">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="4bbab-120">Cuando :::no-loc(cookie)::: no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , usa `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="4bbab-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="4bbab-121">La :::no-loc(cookie)::: propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4bbab-122">Se permiten las autenticaciones :::no-loc(cookie)::: cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4bbab-123">Para obtener más información, vea <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="4bbab-124">En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` para establecer la `HttpContext.User` propiedad y ejecutar el middleware de autorización para las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="4bbab-125">Llame a `UseAuthentication` los `UseAuthorization` métodos y antes de llamar a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="4bbab-126">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4bbab-127">Establezca `:::no-loc(Cookie):::AuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="4bbab-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4bbab-128">:::no-loc(Cookie)::: Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="4bbab-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="4bbab-129">El [ :::no-loc(Cookie)::: middleware de directivas](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) habilita las capacidades de la :::no-loc(cookie)::: Directiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="4bbab-130">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="4bbab-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="4bbab-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> proporcionado al middleware de la :::no-loc(Cookie)::: Directiva para controlar las características globales de :::no-loc(cookie)::: procesamiento y enlazar los :::no-loc(cookie)::: controladores de procesamiento cuando :::no-loc(cookie)::: se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="4bbab-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="4bbab-132">El <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4bbab-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4bbab-133">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4bbab-134">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de :::no-loc(cookie)::: seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4bbab-135">La :::no-loc(Cookie)::: configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `:::no-loc(Cookie):::.SameSite` en `:::no-loc(Cookie):::AuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4bbab-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4bbab-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4bbab-137">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="4bbab-138">Resultado :::no-loc(Cookie)::: . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4bbab-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-139">SameSiteMode.None</span></span>     | <span data-ttu-id="4bbab-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-140">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-143">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4bbab-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-147">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4bbab-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-154">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4bbab-160">Crear una autenticación :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4bbab-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="4bbab-161">Para crear una :::no-loc(cookie)::: información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4bbab-162">La información del usuario se serializa y se almacena en el :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="4bbab-163">Cree un <xref:System.Security.Claims.Claims:::no-loc(Identity):::> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="4bbab-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="4bbab-164">`SignInAsync` crea un cifrado :::no-loc(cookie)::: y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="4bbab-165">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> solo se usa en algunas rutas de acceso específicas de forma predeterminada, por ejemplo, las rutas de acceso de inicio de sesión y de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="4bbab-167">Para obtener más información, vea el [ :::no-loc(Cookie)::: origen de AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="4bbab-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="4bbab-168">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4bbab-169">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4bbab-170">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4bbab-170">Sign out</span></span>

<span data-ttu-id="4bbab-171">Para cerrar la sesión del usuario actual y eliminar su :::no-loc(cookie)::: , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4bbab-172">Si `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (o " :::no-loc(Cookie)::: s") no se usa como esquema (por ejemplo, "Contoso :::no-loc(Cookie)::: "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4bbab-173">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-174">Cuando el explorador se cierra automáticamente, se eliminan los elementos basados en sesión :::no-loc(cookie)::: (no persistentes :::no-loc(cookie)::: ), pero no :::no-loc(cookie)::: se borran los s cuando se cierra una pestaña individual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="4bbab-175">El servidor no recibe notificaciones de eventos de cierre de pestaña o de explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4bbab-176">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="4bbab-176">React to back-end changes</span></span>

<span data-ttu-id="4bbab-177">Una vez que :::no-loc(cookie)::: se crea un, :::no-loc(cookie)::: es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="4bbab-178">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="4bbab-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4bbab-179">El sistema de autenticación de la aplicación :::no-loc(cookie)::: continúa procesando las solicitudes en función de la autenticación :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="4bbab-180">El usuario permanecerá conectado en la aplicación siempre que la autenticación :::no-loc(cookie)::: sea válida.</span><span class="sxs-lookup"><span data-stu-id="4bbab-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="4bbab-181">El <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la :::no-loc(cookie)::: identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="4bbab-182">La validación de :::no-loc(cookie)::: en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4bbab-183">Un enfoque de :::no-loc(cookie)::: validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4bbab-184">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es :::no-loc(cookie)::: necesario volver a autenticar al usuario si :::no-loc(cookie)::: todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="4bbab-185">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4bbab-186">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4bbab-187">Para invalidar un :::no-loc(cookie)::: cuando la base de datos cambie según el `LastChanged` valor, cree el objeto :::no-loc(cookie)::: con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="4bbab-188">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="4bbab-189">El siguiente es un ejemplo de implementación de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4bbab-190">Registre la instancia de eventos durante :::no-loc(cookie)::: el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="4bbab-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4bbab-191">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `Custom:::no-loc(Cookie):::AuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="4bbab-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="4bbab-192">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4bbab-193">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4bbab-194">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="4bbab-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4bbab-195">La validación :::no-loc(cookie)::: de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4bbab-196">:::no-loc(cookie):::Persistentes</span><span class="sxs-lookup"><span data-stu-id="4bbab-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="4bbab-197">Puede que desee que el se :::no-loc(cookie)::: conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="4bbab-198">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4bbab-199">El fragmento de código siguiente crea una identidad y la correspondiente :::no-loc(cookie)::: que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="4bbab-200">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4bbab-201">Si :::no-loc(cookie)::: expira mientras se cierra el explorador, el explorador lo borra :::no-loc(cookie)::: una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="4bbab-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="4bbab-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4bbab-203">:::no-loc(cookie):::Expiración absoluta</span><span class="sxs-lookup"><span data-stu-id="4bbab-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="4bbab-204">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4bbab-205">Para crear un persistente :::no-loc(cookie)::: , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="4bbab-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4bbab-206">De lo contrario, :::no-loc(cookie)::: se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="4bbab-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4bbab-207">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="4bbab-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4bbab-208">El fragmento de código siguiente crea una identidad y la correspondiente :::no-loc(cookie)::: que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="4bbab-209">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bbab-210">:::no-loc(ASP.NET Core Identity)::: es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión.</span><span class="sxs-lookup"><span data-stu-id="4bbab-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4bbab-211">Sin embargo, :::no-loc(cookie)::: se puede usar un proveedor de autenticación basado en :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="4bbab-212">Para obtener más información, vea <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4bbab-213">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bbab-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4bbab-214">Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4bbab-215">Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4bbab-216">El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4bbab-217">En un ejemplo real, el usuario se autenticaría en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4bbab-218">Configuración</span><span class="sxs-lookup"><span data-stu-id="4bbab-218">Configuration</span></span>

<span data-ttu-id="4bbab-219">Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. :::no-loc(Cookie)::: paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="4bbab-220">En el `Startup.ConfigureServices` método, cree el servicio de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> métodos y:</span><span class="sxs-lookup"><span data-stu-id="4bbab-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="4bbab-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4bbab-222">`AuthenticationScheme` resulta útil cuando hay varias instancias de :::no-loc(cookie)::: autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4bbab-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4bbab-223">Si `AuthenticationScheme` se establece en [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " :::no-loc(Cookie)::: s" para el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="4bbab-224">Puede proporcionar cualquier valor de cadena que distinga el esquema.</span><span class="sxs-lookup"><span data-stu-id="4bbab-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4bbab-225">El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="4bbab-226">Cuando :::no-loc(cookie)::: no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , usa `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="4bbab-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="4bbab-227">La :::no-loc(cookie)::: propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> se establece en de `true` forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4bbab-228">Se permiten las autenticaciones :::no-loc(cookie)::: cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4bbab-229">Para obtener más información, vea <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="4bbab-230">En el `Startup.Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4bbab-231">Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="4bbab-232">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4bbab-233">Establezca `:::no-loc(Cookie):::AuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="4bbab-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4bbab-234">:::no-loc(Cookie)::: Middleware de directivas</span><span class="sxs-lookup"><span data-stu-id="4bbab-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="4bbab-235">El [ :::no-loc(Cookie)::: middleware de directivas](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) habilita las capacidades de la :::no-loc(cookie)::: Directiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="4bbab-236">Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.</span><span class="sxs-lookup"><span data-stu-id="4bbab-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="4bbab-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> proporcionado al middleware de la :::no-loc(Cookie)::: Directiva para controlar las características globales de :::no-loc(cookie)::: procesamiento y enlazar los :::no-loc(cookie)::: controladores de procesamiento cuando :::no-loc(cookie)::: se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="4bbab-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="4bbab-238">El <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4bbab-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4bbab-239">Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4bbab-240">Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de :::no-loc(cookie)::: seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4bbab-241">La :::no-loc(Cookie)::: configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `:::no-loc(Cookie):::.SameSite` en `:::no-loc(Cookie):::AuthenticationOptions` la configuración de acuerdo con la matriz siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4bbab-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4bbab-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4bbab-243">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="4bbab-244">Resultado :::no-loc(Cookie)::: . Configuración de SameSite</span><span class="sxs-lookup"><span data-stu-id="4bbab-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4bbab-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-245">SameSiteMode.None</span></span>     | <span data-ttu-id="4bbab-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-246">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-249">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4bbab-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-253">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4bbab-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4bbab-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4bbab-260">SameSiteMode.None</span></span><br><span data-ttu-id="4bbab-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4bbab-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="4bbab-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="4bbab-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="4bbab-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4bbab-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4bbab-266">Crear una autenticación :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4bbab-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="4bbab-267">Para crear una :::no-loc(cookie)::: información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4bbab-268">La información del usuario se serializa y se almacena en el :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="4bbab-269">Cree un <xref:System.Security.Claims.Claims:::no-loc(Identity):::> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:</span><span class="sxs-lookup"><span data-stu-id="4bbab-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4bbab-270">`SignInAsync` crea un cifrado :::no-loc(cookie)::: y lo agrega a la respuesta actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="4bbab-271">Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4bbab-272">El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4bbab-273">En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4bbab-274">Cerrar sesión</span><span class="sxs-lookup"><span data-stu-id="4bbab-274">Sign out</span></span>

<span data-ttu-id="4bbab-275">Para cerrar la sesión del usuario actual y eliminar su :::no-loc(cookie)::: , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4bbab-276">Si `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (o " :::no-loc(Cookie)::: s") no se usa como esquema (por ejemplo, "Contoso :::no-loc(Cookie)::: "), proporcione el esquema que se usa al configurar el proveedor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4bbab-277">De lo contrario, se usa el esquema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4bbab-278">Reaccionar a los cambios de back-end</span><span class="sxs-lookup"><span data-stu-id="4bbab-278">React to back-end changes</span></span>

<span data-ttu-id="4bbab-279">Una vez que :::no-loc(cookie)::: se crea un, :::no-loc(cookie)::: es el único origen de identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="4bbab-280">Si una cuenta de usuario está deshabilitada en los sistemas de servidor:</span><span class="sxs-lookup"><span data-stu-id="4bbab-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4bbab-281">El sistema de autenticación de la aplicación :::no-loc(cookie)::: continúa procesando las solicitudes en función de la autenticación :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4bbab-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="4bbab-282">El usuario permanecerá conectado en la aplicación siempre que la autenticación :::no-loc(cookie)::: sea válida.</span><span class="sxs-lookup"><span data-stu-id="4bbab-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="4bbab-283">El <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la :::no-loc(cookie)::: identidad.</span><span class="sxs-lookup"><span data-stu-id="4bbab-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="4bbab-284">La validación de :::no-loc(cookie)::: en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4bbab-285">Un enfoque de :::no-loc(cookie)::: validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="4bbab-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4bbab-286">Si no se ha cambiado la base de datos desde que se emitió el usuario, no es :::no-loc(cookie)::: necesario volver a autenticar al usuario si :::no-loc(cookie)::: todavía es válido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="4bbab-287">En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4bbab-288">Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4bbab-289">Para invalidar un :::no-loc(cookie)::: cuando la base de datos cambie según el `LastChanged` valor, cree el objeto :::no-loc(cookie)::: con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="4bbab-290">Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="4bbab-291">El siguiente es un ejemplo de implementación de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4bbab-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4bbab-292">Registre la instancia de eventos durante :::no-loc(cookie)::: el registro del servicio en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="4bbab-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4bbab-293">Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `Custom:::no-loc(Cookie):::AuthenticationEvents` clase:</span><span class="sxs-lookup"><span data-stu-id="4bbab-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="4bbab-294">Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4bbab-295">Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4bbab-296">El enfoque descrito aquí se desencadena en cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="4bbab-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4bbab-297">La validación :::no-loc(cookie)::: de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bbab-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4bbab-298">:::no-loc(cookie):::Persistentes</span><span class="sxs-lookup"><span data-stu-id="4bbab-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="4bbab-299">Puede que desee que el se :::no-loc(cookie)::: conserve entre las sesiones del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="4bbab-300">Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4bbab-301">El fragmento de código siguiente crea una identidad y la correspondiente :::no-loc(cookie)::: que sobrevive a través de los cierres del explorador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="4bbab-302">Se respetan los valores de expiración que se hayan configurado previamente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4bbab-303">Si :::no-loc(cookie)::: expira mientras se cierra el explorador, el explorador lo borra :::no-loc(cookie)::: una vez que se reinicia.</span><span class="sxs-lookup"><span data-stu-id="4bbab-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="4bbab-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4bbab-305">:::no-loc(cookie):::Expiración absoluta</span><span class="sxs-lookup"><span data-stu-id="4bbab-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="4bbab-306">Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4bbab-307">Para crear un persistente :::no-loc(cookie)::: , `IsPersistent` también se debe establecer.</span><span class="sxs-lookup"><span data-stu-id="4bbab-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4bbab-308">De lo contrario, :::no-loc(cookie)::: se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene.</span><span class="sxs-lookup"><span data-stu-id="4bbab-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4bbab-309">Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> , si se establece.</span><span class="sxs-lookup"><span data-stu-id="4bbab-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4bbab-310">El fragmento de código siguiente crea una identidad y la correspondiente :::no-loc(cookie)::: que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="4bbab-311">Esto omite cualquier configuración de expiración variable previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
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
