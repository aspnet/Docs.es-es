---
title: Multi-factor Authentication en ASP.NET Core
author: damienbod
description: Aprenda a configurar multi-factor Authentication (MFA) en una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060396"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="6d89b-103">Multi-factor Authentication en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d89b-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="6d89b-104">Por [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="6d89b-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="6d89b-105">Ver o descargar el código de ejemplo (repositorio de GitHub de damienbod/AspNetCoreHybridFlowWithApi)</span><span class="sxs-lookup"><span data-stu-id="6d89b-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="6d89b-106">Multi-factor Authentication (MFA) es un proceso en el que se solicita un usuario durante un evento de inicio de sesión para otras formas de identificación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="6d89b-107">Este mensaje puede indicar un código de un teléfono móvil, usar una clave FIDO2 o proporcionar un análisis de huellas digitales.</span><span class="sxs-lookup"><span data-stu-id="6d89b-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="6d89b-108">Cuando se requiere una segunda forma de autenticación, se mejora la seguridad.</span><span class="sxs-lookup"><span data-stu-id="6d89b-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="6d89b-109">Un atacante no obtiene ni duplica fácilmente el factor adicional.</span><span class="sxs-lookup"><span data-stu-id="6d89b-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="6d89b-110">En este artículo se tratan las siguientes áreas:</span><span class="sxs-lookup"><span data-stu-id="6d89b-110">This article covers the following areas:</span></span>

* <span data-ttu-id="6d89b-111">¿Qué es MFA y qué flujos de MFA se recomiendan?</span><span class="sxs-lookup"><span data-stu-id="6d89b-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="6d89b-112">Configurar MFA para páginas de administración mediante ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="6d89b-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="6d89b-113">Envío del requisito de inicio de sesión de MFA al servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="6d89b-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="6d89b-114">Forzar ASP.NET Core cliente de OpenID Connect para que requiera MFA</span><span class="sxs-lookup"><span data-stu-id="6d89b-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="6d89b-115">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="6d89b-115">MFA, 2FA</span></span>

<span data-ttu-id="6d89b-116">MFA requiere al menos dos o más tipos de pruebas para una identidad como algo que conoce, algo que posee o validación biométrica para que el usuario se autentique.</span><span class="sxs-lookup"><span data-stu-id="6d89b-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="6d89b-117">La autenticación en dos fases (2FA) es como un subconjunto de MFA, pero la diferencia es que MFA puede requerir dos o más factores para demostrar la identidad.</span><span class="sxs-lookup"><span data-stu-id="6d89b-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="6d89b-118">TOTP de MFA (algoritmo de contraseña de un solo tiempo basado en el tiempo)</span><span class="sxs-lookup"><span data-stu-id="6d89b-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="6d89b-119">MFA con TOTP es una implementación compatible mediante ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6d89b-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="6d89b-120">Se puede usar junto con cualquier aplicación de autenticador compatible, lo que incluye:</span><span class="sxs-lookup"><span data-stu-id="6d89b-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="6d89b-121">Aplicación Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="6d89b-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="6d89b-122">Aplicación de Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="6d89b-122">Google Authenticator App</span></span>

<span data-ttu-id="6d89b-123">Vea el siguiente vínculo para obtener información detallada sobre la implementación:</span><span class="sxs-lookup"><span data-stu-id="6d89b-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="6d89b-124">Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d89b-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="6d89b-125">FIDO2 de MFA o no tiene contraseña</span><span class="sxs-lookup"><span data-stu-id="6d89b-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="6d89b-126">FIDO2 es actualmente:</span><span class="sxs-lookup"><span data-stu-id="6d89b-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="6d89b-127">La forma más segura de lograr MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="6d89b-128">El único flujo de MFA que protege contra ataques de suplantación de identidad (phishing).</span><span class="sxs-lookup"><span data-stu-id="6d89b-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="6d89b-129">En la actualidad, ASP.NET Core no es compatible directamente con FIDO2.</span><span class="sxs-lookup"><span data-stu-id="6d89b-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="6d89b-130">FIDO2 se puede usar para MFA o flujos sin contraseña.</span><span class="sxs-lookup"><span data-stu-id="6d89b-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="6d89b-131">Azure Active Directory proporciona compatibilidad con FIDO2 y flujos con contraseña.</span><span class="sxs-lookup"><span data-stu-id="6d89b-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="6d89b-132">Para obtener más información, consulte [Opciones de autenticación con contraseñas para Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="6d89b-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="6d89b-133">SMS DE MFA</span><span class="sxs-lookup"><span data-stu-id="6d89b-133">MFA SMS</span></span>

<span data-ttu-id="6d89b-134">MFA con SMS aumenta la seguridad de forma masiva en comparación con la autenticación de contraseña (factor único).</span><span class="sxs-lookup"><span data-stu-id="6d89b-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="6d89b-135">Sin embargo, ya no se recomienda usar SMS como segundo factor.</span><span class="sxs-lookup"><span data-stu-id="6d89b-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="6d89b-136">Existen demasiados vectores de ataque conocidos para este tipo de implementación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="6d89b-137">Directrices para NIST</span><span class="sxs-lookup"><span data-stu-id="6d89b-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="6d89b-138">Configurar MFA para páginas de administración mediante ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="6d89b-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="6d89b-139">MFA podría verse obligado a los usuarios para acceder a las páginas confidenciales dentro de una ASP.NET Core Identity aplicación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="6d89b-140">Esto puede ser útil para las aplicaciones en las que existen distintos niveles de acceso para las distintas identidades.</span><span class="sxs-lookup"><span data-stu-id="6d89b-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="6d89b-141">Por ejemplo, es posible que los usuarios puedan ver los datos del perfil mediante un inicio de sesión de contraseña, pero es necesario que un administrador use MFA para tener acceso a las páginas administrativas.</span><span class="sxs-lookup"><span data-stu-id="6d89b-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="6d89b-142">Extensión del inicio de sesión con una notificaciones de MFA</span><span class="sxs-lookup"><span data-stu-id="6d89b-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="6d89b-143">El código de demostración se configura mediante ASP.NET Core Identity páginas con y Razor .</span><span class="sxs-lookup"><span data-stu-id="6d89b-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="6d89b-144">El `AddIdentity` método se usa en lugar de `AddDefaultIdentity` uno, por lo que se `IUserClaimsPrincipalFactory` puede usar una implementación para agregar notificaciones a la identidad después de un inicio de sesión correcto.</span><span class="sxs-lookup"><span data-stu-id="6d89b-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="6d89b-145">La `AdditionalUserClaimsPrincipalFactory` clase agrega la `amr` notificación a las notificaciones de usuario solo después de un inicio de sesión correcto.</span><span class="sxs-lookup"><span data-stu-id="6d89b-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="6d89b-146">El valor de la demanda se lee de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6d89b-146">The claim's value is read from the database.</span></span> <span data-ttu-id="6d89b-147">La demanda se agrega aquí porque el usuario solo debe tener acceso a la vista protegida más alta si la identidad ha iniciado sesión con MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="6d89b-148">Si la vista de base de datos se lee directamente en la base de datos en lugar de usar la notificaciones, es posible acceder a la vista sin MFA directamente después de activar la MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="6d89b-149">Dado Identity que la configuración del servicio ha cambiado en la `Startup` clase, los diseños de Identity deben actualizarse.</span><span class="sxs-lookup"><span data-stu-id="6d89b-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="6d89b-150">Scaffolding las Identity páginas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="6d89b-151">Defina el diseño en el archivo *Identity /account/Manage/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6d89b-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="6d89b-152">Asigne también el diseño de todas las páginas de administración de las Identity páginas:</span><span class="sxs-lookup"><span data-stu-id="6d89b-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="6d89b-153">Validar el requisito de MFA en la página de administración</span><span class="sxs-lookup"><span data-stu-id="6d89b-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="6d89b-154">La página de administración Razor valida que el usuario ha iniciado sesión con MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="6d89b-155">En el `OnGet` método, la identidad se usa para tener acceso a las notificaciones del usuario.</span><span class="sxs-lookup"><span data-stu-id="6d89b-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="6d89b-156">`amr`Se comprueba el valor de la demanda `mfa` .</span><span class="sxs-lookup"><span data-stu-id="6d89b-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="6d89b-157">Si la identidad no tiene esta demanda o es `false` , la página redirige a la página habilitar MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="6d89b-158">Esto es posible porque el usuario ya ha iniciado sesión, pero sin MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-158">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="6d89b-159">Lógica de la interfaz de usuario para alternar la información de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="6d89b-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="6d89b-160">En el inicio se ha agregado una directiva de autorización.</span><span class="sxs-lookup"><span data-stu-id="6d89b-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="6d89b-161">La Directiva requiere la `amr` demanda con el valor `mfa` .</span><span class="sxs-lookup"><span data-stu-id="6d89b-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="6d89b-162">Esta Directiva se puede usar en la `_Layout` vista para mostrar u ocultar el menú de **Administración** con la ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="6d89b-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="6d89b-163">Si la identidad ha iniciado sesión con MFA, el menú de **Administración** se muestra sin la advertencia de la información sobre herramientas.</span><span class="sxs-lookup"><span data-stu-id="6d89b-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="6d89b-164">Cuando el usuario ha iniciado sesión sin MFA, se muestra el menú **Administrador (no habilitado)** junto con la información sobre herramientas que informa al usuario (explicando la advertencia).</span><span class="sxs-lookup"><span data-stu-id="6d89b-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="6d89b-165">Si el usuario inicia sesión sin MFA, se muestra la ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="6d89b-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Autenticación MFA de administrador](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="6d89b-167">Al hacer clic en el vínculo de **Administrador** , se redirige al usuario a la vista de habilitación de MFA:</span><span class="sxs-lookup"><span data-stu-id="6d89b-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![El administrador activa la autenticación MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="6d89b-169">Envío del requisito de inicio de sesión de MFA al servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="6d89b-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="6d89b-170">El `acr_values` parámetro se puede utilizar para pasar el `mfa` valor necesario desde el cliente al servidor en una solicitud de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="6d89b-171">El `acr_values` parámetro debe administrarse en el servidor OpenID Connect para que funcione.</span><span class="sxs-lookup"><span data-stu-id="6d89b-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="6d89b-172">Cliente de ASP.NET Core OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="6d89b-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="6d89b-173">La Razor aplicación de cliente de OpenID Connect de las páginas ASP.net Core usa el `AddOpenIdConnect` método para iniciar sesión en el servidor de OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="6d89b-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="6d89b-174">El `acr_values` parámetro se establece con el `mfa` valor y se envía con la solicitud de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="6d89b-175">`OpenIdConnectEvents`Se utiliza para agregar este.</span><span class="sxs-lookup"><span data-stu-id="6d89b-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="6d89b-176">Para obtener `acr_values` los valores de parámetro recomendados, consulte [valores de referencia del método de autenticación](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="6d89b-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="6d89b-177">Ejemplo Identity de servidor OpenID Connect 4 con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="6d89b-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="6d89b-178">En el servidor de OpenID Connect, que se implementa mediante las ASP.NET Core Identity vistas de MVC, se crea una nueva vista denominada *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6d89b-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="6d89b-179">La vista:</span><span class="sxs-lookup"><span data-stu-id="6d89b-179">The view:</span></span>

* <span data-ttu-id="6d89b-180">Muestra si Identity procede de una aplicación que requiere MFA, pero el usuario no lo activó en Identity .</span><span class="sxs-lookup"><span data-stu-id="6d89b-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="6d89b-181">Informa al usuario y agrega un vínculo para activarlo.</span><span class="sxs-lookup"><span data-stu-id="6d89b-181">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="6d89b-182">En el `Login` método, `IIdentityServerInteractionService` se usa la implementación de la interfaz `_interaction` para tener acceso a los parámetros de solicitud de OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="6d89b-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="6d89b-183">`acr_values`Se tiene acceso al parámetro mediante la `AcrValues` propiedad.</span><span class="sxs-lookup"><span data-stu-id="6d89b-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="6d89b-184">A medida que el cliente lo envía con `mfa` Set, esto se puede comprobar.</span><span class="sxs-lookup"><span data-stu-id="6d89b-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="6d89b-185">Si se requiere MFA y el usuario de ASP.NET Core Identity tiene habilitado MFA, el inicio de sesión continúa.</span><span class="sxs-lookup"><span data-stu-id="6d89b-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="6d89b-186">Cuando el usuario no tiene MFA habilitado, el usuario se redirige a la vista personalizada *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6d89b-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="6d89b-187">Después, ASP.NET Core Identity inicia la sesión del usuario en.</span><span class="sxs-lookup"><span data-stu-id="6d89b-187">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="6d89b-188">El `ExternalLoginCallback` método funciona como el Identity Inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="6d89b-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="6d89b-189">`AcrValues`Se comprueba la propiedad para el `mfa` valor.</span><span class="sxs-lookup"><span data-stu-id="6d89b-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="6d89b-190">Si el `mfa` valor está presente, se fuerza MFA antes de que se complete el inicio de sesión (por ejemplo, redirigido a la `ErrorEnable2FA` vista).</span><span class="sxs-lookup"><span data-stu-id="6d89b-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="6d89b-191">Si el usuario ya ha iniciado sesión, la aplicación cliente:</span><span class="sxs-lookup"><span data-stu-id="6d89b-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="6d89b-192">Sigue validando la `amr` demanda.</span><span class="sxs-lookup"><span data-stu-id="6d89b-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="6d89b-193">Puede configurar MFA con un vínculo a la ASP.NET Core Identity vista.</span><span class="sxs-lookup"><span data-stu-id="6d89b-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="6d89b-195">Forzar ASP.NET Core cliente de OpenID Connect para que requiera MFA</span><span class="sxs-lookup"><span data-stu-id="6d89b-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="6d89b-196">En este ejemplo se muestra cómo una aplicación de ASP.NET Core Razor página, que usa OpenID Connect para iniciar sesión, puede requerir que los usuarios se autentiquen mediante MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="6d89b-197">Para validar el requisito de MFA, `IAuthorizationRequirement` se crea un requisito.</span><span class="sxs-lookup"><span data-stu-id="6d89b-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="6d89b-198">Esto se agregará a las páginas con una directiva que requiera MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="6d89b-199">`AuthorizationHandler`Se implementa una que utilizará la `amr` demanda y comprobará el valor `mfa` .</span><span class="sxs-lookup"><span data-stu-id="6d89b-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="6d89b-200">`amr`Se devuelve en la `id_token` de una autenticación correcta y puede tener muchos valores distintos según se define en la especificación de [los valores de referencia del método de autenticación](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="6d89b-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="6d89b-201">El valor devuelto depende de cómo se autentique la identidad y en la implementación del servidor OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="6d89b-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="6d89b-202">El `AuthorizationHandler` usa el `RequireMfa` requisito y valida la `amr` solicitud.</span><span class="sxs-lookup"><span data-stu-id="6d89b-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="6d89b-203">El servidor OpenID Connect se puede implementar mediante Identity 4 con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6d89b-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="6d89b-204">Cuando un usuario inicia sesión con TOTP, la `amr` demanda se devuelve con un valor de MFA.</span><span class="sxs-lookup"><span data-stu-id="6d89b-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="6d89b-205">Si usa una implementación de servidor OpenID Connect diferente o un tipo de MFA diferente, la `amr` demanda tendrá o puede tener un valor diferente.</span><span class="sxs-lookup"><span data-stu-id="6d89b-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="6d89b-206">El código debe extenderse para que lo acepte también.</span><span class="sxs-lookup"><span data-stu-id="6d89b-206">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="6d89b-207">En el `Startup.ConfigureServices` método, el `AddOpenIdConnect` método se utiliza como esquema de desafío predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6d89b-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="6d89b-208">El controlador de autorización, que se usa para comprobar la `amr` demanda, se agrega al contenedor de inversión de control.</span><span class="sxs-lookup"><span data-stu-id="6d89b-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="6d89b-209">A continuación, se crea una directiva que agrega el `RequireMfa` requisito.</span><span class="sxs-lookup"><span data-stu-id="6d89b-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="6d89b-210">Esta Directiva se usa entonces en la Razor Página según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="6d89b-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="6d89b-211">También se puede Agregar la Directiva globalmente para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6d89b-211">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="6d89b-212">Si el usuario se autentica sin MFA, es `amr` probable que la demanda tenga un `pwd` valor.</span><span class="sxs-lookup"><span data-stu-id="6d89b-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="6d89b-213">La solicitud no estará autorizada para tener acceso a la página.</span><span class="sxs-lookup"><span data-stu-id="6d89b-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="6d89b-214">Con los valores predeterminados, se redirigirá al usuario a la página *account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="6d89b-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="6d89b-215">Este comportamiento se puede cambiar o puede implementar su propia lógica personalizada aquí.</span><span class="sxs-lookup"><span data-stu-id="6d89b-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="6d89b-216">En este ejemplo, se agrega un vínculo para que el usuario válido pueda configurar MFA para su cuenta.</span><span class="sxs-lookup"><span data-stu-id="6d89b-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="6d89b-217">Ahora solo los usuarios que se autentican con MFA pueden tener acceso a la página o el sitio Web.</span><span class="sxs-lookup"><span data-stu-id="6d89b-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="6d89b-218">Si se usan diferentes tipos de MFA o si 2FA es correcto, la `amr` demanda tendrá valores diferentes y se debe procesar correctamente.</span><span class="sxs-lookup"><span data-stu-id="6d89b-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="6d89b-219">Los diferentes servidores OpenID Connect también devuelven valores diferentes para esta demanda y podrían no seguir la especificación de [los valores de referencia del método de autenticación](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="6d89b-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="6d89b-220">Al iniciar sesión sin MFA (por ejemplo, usando simplemente una contraseña):</span><span class="sxs-lookup"><span data-stu-id="6d89b-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="6d89b-221">`amr`Tiene el `pwd` valor:</span><span class="sxs-lookup"><span data-stu-id="6d89b-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="6d89b-223">Acceso denegado:</span><span class="sxs-lookup"><span data-stu-id="6d89b-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="6d89b-225">También puede iniciar sesión con OTP con Identity :</span><span class="sxs-lookup"><span data-stu-id="6d89b-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="6d89b-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6d89b-227">Additional resources</span></span>

* [<span data-ttu-id="6d89b-228">Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d89b-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="6d89b-229">Opciones de autenticación sin contraseña de Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d89b-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="6d89b-230">FIDO2 biblioteca .NET para FIDO2/atestación de webauthn y aserción mediante .NET</span><span class="sxs-lookup"><span data-stu-id="6d89b-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="6d89b-231">Webauthn maravilla</span><span class="sxs-lookup"><span data-stu-id="6d89b-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
