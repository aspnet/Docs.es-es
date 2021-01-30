---
title: Autorizar con un esquema específico en ASP.NET Core
author: rick-anderson
description: En este artículo se explica cómo limitar la identidad a un esquema específico cuando se trabaja con varios métodos de autenticación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: c4cbec1b829fb8fd47f7b6924b6870bd5dd7097d
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057309"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="932da-103">Autorizar con un esquema específico en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="932da-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="932da-104">Para obtener una introducción a los esquemas de autenticación en ASP.NET Core, consulte [esquema de autenticación](xref:security/authentication/index#authentication-scheme).</span><span class="sxs-lookup"><span data-stu-id="932da-104">For an introduction to authentication schemes in ASP.NET Core, see [Authentication scheme](xref:security/authentication/index#authentication-scheme).</span></span>

<span data-ttu-id="932da-105">En algunos escenarios, como las aplicaciones de una sola página (Spa), es habitual usar varios métodos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="932da-106">Por ejemplo, la aplicación puede usar la cookie autenticación basada en para iniciar sesión y la autenticación de portador de JWT para solicitudes de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="932da-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="932da-107">En algunos casos, la aplicación puede tener varias instancias de un controlador de autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="932da-108">Por ejemplo, dos cookie Controladores donde uno contiene una identidad básica y uno se crea cuando se desencadena una autenticación multifactor (MFA).</span><span class="sxs-lookup"><span data-stu-id="932da-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="932da-109">MFA se puede desencadenar porque el usuario solicitó una operación que requiere seguridad adicional.</span><span class="sxs-lookup"><span data-stu-id="932da-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="932da-110">Para obtener más información sobre cómo aplicar MFA cuando un usuario solicita un recurso que requiere MFA, consulte la sección de protección de problemas de GitHub [con MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="932da-110">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="932da-111">Un esquema de autenticación se denomina cuando el servicio de autenticación se configura durante la autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="932da-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="932da-112">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="932da-113">En el código anterior, se han agregado dos controladores de autenticación: uno para cookie s y otro para el portador.</span><span class="sxs-lookup"><span data-stu-id="932da-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="932da-114">Al especificar el esquema predeterminado, `HttpContext.User` se establece la propiedad en esa identidad.</span><span class="sxs-lookup"><span data-stu-id="932da-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="932da-115">Si no se desea ese comportamiento, deshabilítelo mediante la invocación del formulario sin parámetros de `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="932da-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="932da-116">Seleccionar el esquema con el atributo Authorize</span><span class="sxs-lookup"><span data-stu-id="932da-116">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="932da-117">En el momento de la autorización, la aplicación indica el controlador que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="932da-117">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="932da-118">Seleccione el controlador con el que se autorizará la aplicación pasando una lista delimitada por comas de esquemas de autenticación a `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="932da-118">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="932da-119">El `[Authorize]` atributo especifica el esquema de autenticación o los esquemas que se van a usar independientemente de si se ha configurado un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="932da-119">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="932da-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="932da-120">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="932da-121">En el ejemplo anterior, los cookie controladores de portador y se ejecutan y tienen la oportunidad de crear y anexar una identidad para el usuario actual.</span><span class="sxs-lookup"><span data-stu-id="932da-121">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="932da-122">Al especificar solo un esquema, se ejecuta el controlador correspondiente.</span><span class="sxs-lookup"><span data-stu-id="932da-122">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="932da-123">En el código anterior, solo se ejecuta el controlador con el esquema "portador".</span><span class="sxs-lookup"><span data-stu-id="932da-123">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="932da-124">cookieSe omiten las identidades basadas en.</span><span class="sxs-lookup"><span data-stu-id="932da-124">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="932da-125">Seleccionar el esquema con directivas</span><span class="sxs-lookup"><span data-stu-id="932da-125">Selecting the scheme with policies</span></span>

<span data-ttu-id="932da-126">Si prefiere especificar los esquemas deseados en la [Directiva](xref:security/authorization/policies), puede establecer la `AuthenticationSchemes` colección al agregar la Directiva:</span><span class="sxs-lookup"><span data-stu-id="932da-126">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="932da-127">En el ejemplo anterior, la Directiva "Over18" solo se ejecuta con la identidad creada por el controlador de "portador".</span><span class="sxs-lookup"><span data-stu-id="932da-127">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="932da-128">Use la Directiva estableciendo la `[Authorize]` propiedad del atributo `Policy` :</span><span class="sxs-lookup"><span data-stu-id="932da-128">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="932da-129">Usar varios esquemas de autenticación</span><span class="sxs-lookup"><span data-stu-id="932da-129">Use multiple authentication schemes</span></span>

<span data-ttu-id="932da-130">Es posible que algunas aplicaciones necesiten admitir varios tipos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-130">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="932da-131">Por ejemplo, es posible que la aplicación autentique a los usuarios de Azure Active Directory y de una base de datos de usuarios.</span><span class="sxs-lookup"><span data-stu-id="932da-131">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="932da-132">Otro ejemplo es una aplicación que autentica a los usuarios de Servicios de federación de Active Directory (AD FS) y Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="932da-132">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="932da-133">En este caso, la aplicación debe aceptar un token de portador JWT de varios emisores.</span><span class="sxs-lookup"><span data-stu-id="932da-133">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="932da-134">Agregue todos los esquemas de autenticación que desea aceptar.</span><span class="sxs-lookup"><span data-stu-id="932da-134">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="932da-135">Por ejemplo, el código siguiente en `Startup.ConfigureServices` agrega dos esquemas de autenticación de portador JWT con distintos emisores:</span><span class="sxs-lookup"><span data-stu-id="932da-135">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="932da-136">Solo se registra una autenticación de portador de JWT con el esquema de autenticación predeterminado `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="932da-136">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="932da-137">La autenticación adicional tiene que registrarse con un esquema de autenticación único.</span><span class="sxs-lookup"><span data-stu-id="932da-137">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="932da-138">El siguiente paso consiste en actualizar la Directiva de autorización predeterminada para aceptar ambos esquemas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-138">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="932da-139">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="932da-139">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="932da-140">Como la Directiva de autorización predeterminada se invalida, es posible usar el `[Authorize]` atributo en los controladores.</span><span class="sxs-lookup"><span data-stu-id="932da-140">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="932da-141">Después, el controlador acepta las solicitudes con JWT emitidos por el primer o el segundo emisor.</span><span class="sxs-lookup"><span data-stu-id="932da-141">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="932da-142">Vea [este problema de github sobre el](https://github.com/dotnet/aspnetcore/issues/26002) uso de varios esquemas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="932da-142">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
