---
title: Autorizar con un esquema específico en ASP.NET Core
author: rick-anderson
description: En este artículo se explica cómo limitar la identidad a un esquema específico cuando se trabaja con varios métodos de autenticación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: a5f2dff7b0e0d4f209ba445b2efb6fb261cbaab1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464021"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="dedc4-103">Autorizar con un esquema específico en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedc4-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="dedc4-104">En algunos escenarios, como las aplicaciones de una sola página (Spa), es habitual usar varios métodos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="dedc4-105">Por ejemplo, la aplicación puede usar la :::no-loc(cookie)::: autenticación basada en para iniciar sesión y la autenticación de portador de JWT para solicitudes de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dedc4-105">For example, the app may use :::no-loc(cookie):::-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="dedc4-106">En algunos casos, la aplicación puede tener varias instancias de un controlador de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="dedc4-107">Por ejemplo, dos :::no-loc(cookie)::: Controladores donde uno contiene una identidad básica y uno se crea cuando se desencadena una autenticación multifactor (MFA).</span><span class="sxs-lookup"><span data-stu-id="dedc4-107">For example, two :::no-loc(cookie)::: handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="dedc4-108">MFA se puede desencadenar porque el usuario solicitó una operación que requiere seguridad adicional.</span><span class="sxs-lookup"><span data-stu-id="dedc4-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="dedc4-109">Para obtener más información sobre cómo aplicar MFA cuando un usuario solicita un recurso que requiere MFA, consulte la sección de protección de problemas de GitHub [con MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="dedc4-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="dedc4-110">Un esquema de autenticación se denomina cuando el servicio de autenticación se configura durante la autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="dedc4-111">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="dedc4-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .Add:::no-loc(Cookie):::(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="dedc4-112">En el código anterior, se han agregado dos controladores de autenticación: uno para :::no-loc(cookie)::: s y otro para el portador.</span><span class="sxs-lookup"><span data-stu-id="dedc4-112">In the preceding code, two authentication handlers have been added: one for :::no-loc(cookie):::s and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="dedc4-113">Al especificar el esquema predeterminado, `HttpContext.User` se establece la propiedad en esa identidad.</span><span class="sxs-lookup"><span data-stu-id="dedc4-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="dedc4-114">Si no se desea ese comportamiento, deshabilítelo mediante la invocación del formulario sin parámetros de `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="dedc4-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="dedc4-115">Seleccionar el esquema con el atributo Authorize</span><span class="sxs-lookup"><span data-stu-id="dedc4-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="dedc4-116">En el momento de la autorización, la aplicación indica el controlador que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="dedc4-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="dedc4-117">Seleccione el controlador con el que se autorizará la aplicación pasando una lista delimitada por comas de esquemas de autenticación a `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="dedc4-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="dedc4-118">El `[Authorize]` atributo especifica el esquema de autenticación o los esquemas que se van a usar independientemente de si se ha configurado un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="dedc4-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="dedc4-119">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="dedc4-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="dedc4-120">En el ejemplo anterior, los :::no-loc(cookie)::: controladores de portador y se ejecutan y tienen la oportunidad de crear y anexar una identidad para el usuario actual.</span><span class="sxs-lookup"><span data-stu-id="dedc4-120">In the preceding example, both the :::no-loc(cookie)::: and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="dedc4-121">Al especificar solo un esquema, se ejecuta el controlador correspondiente.</span><span class="sxs-lookup"><span data-stu-id="dedc4-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="dedc4-122">En el código anterior, solo se ejecuta el controlador con el esquema "portador".</span><span class="sxs-lookup"><span data-stu-id="dedc4-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="dedc4-123">:::no-loc(cookie):::Se omiten las identidades basadas en.</span><span class="sxs-lookup"><span data-stu-id="dedc4-123">Any :::no-loc(cookie):::-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="dedc4-124">Seleccionar el esquema con directivas</span><span class="sxs-lookup"><span data-stu-id="dedc4-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="dedc4-125">Si prefiere especificar los esquemas deseados en la [Directiva](xref:security/authorization/policies), puede establecer la `AuthenticationSchemes` colección al agregar la Directiva:</span><span class="sxs-lookup"><span data-stu-id="dedc4-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="dedc4-126">En el ejemplo anterior, la Directiva "Over18" solo se ejecuta con la identidad creada por el controlador de "portador".</span><span class="sxs-lookup"><span data-stu-id="dedc4-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="dedc4-127">Use la Directiva estableciendo la `[Authorize]` propiedad del atributo `Policy` :</span><span class="sxs-lookup"><span data-stu-id="dedc4-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="dedc4-128">Usar varios esquemas de autenticación</span><span class="sxs-lookup"><span data-stu-id="dedc4-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="dedc4-129">Es posible que algunas aplicaciones necesiten admitir varios tipos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="dedc4-130">Por ejemplo, es posible que la aplicación autentique a los usuarios de Azure Active Directory y de una base de datos de usuarios.</span><span class="sxs-lookup"><span data-stu-id="dedc4-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="dedc4-131">Otro ejemplo es una aplicación que autentica a los usuarios de Servicios de federación de Active Directory (AD FS) y Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="dedc4-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="dedc4-132">En este caso, la aplicación debe aceptar un token de portador JWT de varios emisores.</span><span class="sxs-lookup"><span data-stu-id="dedc4-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="dedc4-133">Agregue todos los esquemas de autenticación que desea aceptar.</span><span class="sxs-lookup"><span data-stu-id="dedc4-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="dedc4-134">Por ejemplo, el código siguiente en `Startup.ConfigureServices` agrega dos esquemas de autenticación de portador JWT con distintos emisores:</span><span class="sxs-lookup"><span data-stu-id="dedc4-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="dedc4-135">Solo se registra una autenticación de portador de JWT con el esquema de autenticación predeterminado `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="dedc4-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="dedc4-136">La autenticación adicional tiene que registrarse con un esquema de autenticación único.</span><span class="sxs-lookup"><span data-stu-id="dedc4-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="dedc4-137">El siguiente paso consiste en actualizar la Directiva de autorización predeterminada para aceptar ambos esquemas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="dedc4-138">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="dedc4-138">For example:</span></span>

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

<span data-ttu-id="dedc4-139">Como la Directiva de autorización predeterminada se invalida, es posible usar el `[Authorize]` atributo en los controladores.</span><span class="sxs-lookup"><span data-stu-id="dedc4-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="dedc4-140">Después, el controlador acepta las solicitudes con JWT emitidos por el primer o el segundo emisor.</span><span class="sxs-lookup"><span data-stu-id="dedc4-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="dedc4-141">Vea [este problema de github sobre el](https://github.com/dotnet/aspnetcore/issues/26002) uso de varios esquemas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="dedc4-141">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
