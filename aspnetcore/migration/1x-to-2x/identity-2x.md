---
title: 'Migración de la autenticación y :::no-loc(Identity)::: a ASP.NET Core 2,0'
author: scottaddie
description: 'En este artículo se describen los pasos más comunes para migrar la autenticación ASP.NET Core 1. x y :::no-loc(Identity)::: ASP.NET Core 2,0.'
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057185"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="8dbbb-103">Migración de la autenticación y :::no-loc(Identity)::: a ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="8dbbb-103">Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0</span></span>

<span data-ttu-id="8dbbb-104">Por [Scott Addie](https://github.com/scottaddie) y [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="8dbbb-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="8dbbb-105">ASP.NET Core 2,0 tiene un nuevo modelo de autenticación y [:::no-loc(Identity):::](xref:security/authentication/identity) que simplifica la configuración mediante servicios.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-105">ASP.NET Core 2.0 has a new model for authentication and [:::no-loc(Identity):::](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="8dbbb-106">ASP.NET Core 1. x aplicaciones que usan la autenticación de o :::no-loc(Identity)::: se pueden actualizar para usar el nuevo modelo, tal y como se describe a continuación.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-106">ASP.NET Core 1.x applications that use authentication or :::no-loc(Identity)::: can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="8dbbb-107">Actualizar espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="8dbbb-107">Update namespaces</span></span>

<span data-ttu-id="8dbbb-108">En 1. x, se han `:::no-loc(Identity):::Role` encontrado clases como y `:::no-loc(Identity):::User` en el `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-108">In 1.x, classes such `:::no-loc(Identity):::Role` and `:::no-loc(Identity):::User` were found in the `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="8dbbb-109">En 2,0, el <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> espacio de nombres se convirtió en el nuevo inicio de algunas de estas clases.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-109">In 2.0, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="8dbbb-110">Con el :::no-loc(Identity)::: código predeterminado, las clases afectadas incluyen `ApplicationUser` y `Startup` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-110">With the default :::no-loc(Identity)::: code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="8dbbb-111">Ajuste las `using` instrucciones para resolver las referencias afectadas.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="8dbbb-112">Middleware de autenticación y servicios</span><span class="sxs-lookup"><span data-stu-id="8dbbb-112">Authentication Middleware and services</span></span>

<span data-ttu-id="8dbbb-113">En los proyectos de 1. x, la autenticación se configura mediante middleware.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="8dbbb-114">Se invoca un método de middleware para cada esquema de autenticación que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="8dbbb-115">En el siguiente ejemplo de 1. x se configura la autenticación :::no-loc(Identity)::: de Facebook con en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-115">The following 1.x example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.Use:::no-loc(Identity):::();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="8dbbb-116">En los proyectos de 2,0, la autenticación se configura a través de los servicios.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="8dbbb-117">Cada esquema de autenticación se registra en el `ConfigureServices` método de *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="8dbbb-118">El `Use:::no-loc(Identity):::` método se reemplaza por `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-118">The `Use:::no-loc(Identity):::` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="8dbbb-119">En el siguiente ejemplo de 2,0 se configura la autenticación :::no-loc(Identity)::: de Facebook con en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-119">The following 2.0 example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak :::no-loc(Identity)::: :::no-loc(cookie):::s, they're no longer part of :::no-loc(Identity):::Options.
    services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="8dbbb-120">El `UseAuthentication` método agrega un componente de middleware de autenticación única, que es responsable de la autenticación automática y el control de las solicitudes de autenticación remota.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="8dbbb-121">Reemplaza todos los componentes de middleware individuales por un único componente común de middleware.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="8dbbb-122">A continuación se muestran 2,0 instrucciones de migración para cada esquema de autenticación principal.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="8dbbb-123">:::no-loc(Cookie):::autenticación basada en</span><span class="sxs-lookup"><span data-stu-id="8dbbb-123">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="8dbbb-124">Seleccione una de las dos opciones siguientes y realice los cambios necesarios en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="8dbbb-125">Usar :::no-loc(cookie)::: s con :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="8dbbb-125">Use :::no-loc(cookie):::s with :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="8dbbb-126">Reemplace `Use:::no-loc(Identity):::` por `UseAuthentication` en el `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-126">Replace `Use:::no-loc(Identity):::` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="8dbbb-127">Invoque el `Add:::no-loc(Identity):::` método en el `ConfigureServices` método para agregar los :::no-loc(cookie)::: servicios de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-127">Invoke the `Add:::no-loc(Identity):::` method in the `ConfigureServices` method to add the :::no-loc(cookie)::: authentication services.</span></span>
    - <span data-ttu-id="8dbbb-128">Opcionalmente, invoque `ConfigureApplication:::no-loc(Cookie):::` el `ConfigureExternal:::no-loc(Cookie):::` método o en el `ConfigureServices` método para ajustar la :::no-loc(Identity)::: :::no-loc(cookie)::: configuración.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-128">Optionally, invoke the `ConfigureApplication:::no-loc(Cookie):::` or `ConfigureExternal:::no-loc(Cookie):::` method in the `ConfigureServices` method to tweak the :::no-loc(Identity)::: :::no-loc(cookie)::: settings.</span></span>

        ```csharp
        services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="8dbbb-129">Usar :::no-loc(cookie)::: s sin :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="8dbbb-129">Use :::no-loc(cookie):::s without :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="8dbbb-130">Reemplace la `Use:::no-loc(Cookie):::Authentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-130">Replace the `Use:::no-loc(Cookie):::Authentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="8dbbb-131">Invoque `AddAuthentication` los `Add:::no-loc(Cookie):::` métodos y en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-131">Invoke the `AddAuthentication` and `Add:::no-loc(Cookie):::` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the :::no-loc(cookie)::: to be automatically authenticated and assigned to HttpContext.User,
        // remove the :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
                .Add:::no-loc(Cookie):::(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="8dbbb-132">Autenticación de portador de JWT</span><span class="sxs-lookup"><span data-stu-id="8dbbb-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="8dbbb-133">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="8dbbb-134">Reemplace la `UseJwtBearerAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-135">Invoque el `AddJwtBearer` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="8dbbb-136">Este fragmento de código no usa :::no-loc(Identity)::: , por lo que el esquema predeterminado se debe establecer pasando `JwtBearerDefaults.AuthenticationScheme` al `AddAuthentication` método.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-136">This code snippet doesn't use :::no-loc(Identity):::, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="8dbbb-137">Autenticación de OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="8dbbb-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="8dbbb-138">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="8dbbb-139">Reemplace la `UseOpenIdConnectAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-140">Invoque el `AddOpenIdConnect` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="8dbbb-141">Reemplace la `PostLogoutRedirectUri` propiedad en la `OpenIdConnectOptions` acción por `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="8dbbb-142">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="8dbbb-142">Facebook authentication</span></span>

<span data-ttu-id="8dbbb-143">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="8dbbb-144">Reemplace la `UseFacebookAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-145">Invoque el `AddFacebook` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="8dbbb-146">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="8dbbb-146">Google authentication</span></span>

<span data-ttu-id="8dbbb-147">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="8dbbb-148">Reemplace la `UseGoogleAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-149">Invoque el `AddGoogle` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="8dbbb-150">Autenticación con cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="8dbbb-150">Microsoft Account authentication</span></span>

<span data-ttu-id="8dbbb-151">Para obtener más información sobre la autenticación de cuenta de Microsoft, consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="8dbbb-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="8dbbb-152">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="8dbbb-153">Reemplace la `UseMicrosoftAccountAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-154">Invoque el `AddMicrosoftAccount` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="8dbbb-155">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="8dbbb-155">Twitter authentication</span></span>

<span data-ttu-id="8dbbb-156">Realice los cambios siguientes en *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="8dbbb-157">Reemplace la `UseTwitterAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="8dbbb-158">Invoque el `AddTwitter` método en el `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="8dbbb-159">Establecer esquemas de autenticación predeterminados</span><span class="sxs-lookup"><span data-stu-id="8dbbb-159">Setting default authentication schemes</span></span>

<span data-ttu-id="8dbbb-160">En 1. x, las `AutomaticAuthenticate` `AutomaticChallenge` propiedades y de la clase base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) se debían establecer en un esquema de autenticación único.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="8dbbb-161">No había una buena manera de aplicar esto.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="8dbbb-162">En 2,0, estas dos propiedades se han quitado como propiedades en la `AuthenticationOptions` instancia individual.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="8dbbb-163">Se pueden configurar en la `AddAuthentication` llamada al método en el `ConfigureServices` método de *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="8dbbb-164">En el fragmento de código anterior, el esquema predeterminado se establece en `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="8dbbb-164">In the preceding code snippet, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="8dbbb-165">También puede usar una versión sobrecargada del `AddAuthentication` método para establecer más de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="8dbbb-166">En el siguiente ejemplo de método sobrecargado, el esquema predeterminado se establece en `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-166">In the following overloaded method example, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="8dbbb-167">El esquema de autenticación puede especificarse también en sus `[Authorize]` atributos individuales o en las directivas de autorización.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="8dbbb-168">Defina un esquema predeterminado en 2,0 si se cumple alguna de las siguientes condiciones:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="8dbbb-169">Quiere que el usuario inicie sesión automáticamente</span><span class="sxs-lookup"><span data-stu-id="8dbbb-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="8dbbb-170">El `[Authorize]` atributo o las directivas de autorización se usan sin especificar esquemas.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="8dbbb-171">Una excepción a esta regla es el `Add:::no-loc(Identity):::` método.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-171">An exception to this rule is the `Add:::no-loc(Identity):::` method.</span></span> <span data-ttu-id="8dbbb-172">Este método agrega :::no-loc(cookie)::: s para usted y establece los esquemas de autenticación y desafío predeterminados en la aplicación :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-172">This method adds :::no-loc(cookie):::s for you and sets the default authenticate and challenge schemes to the application :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme`.</span></span> <span data-ttu-id="8dbbb-173">Además, establece el esquema de inicio de sesión predeterminado en el externo :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-173">Additionally, it sets the default sign-in scheme to the external :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="8dbbb-174">Uso de extensiones de autenticación HttpContext</span><span class="sxs-lookup"><span data-stu-id="8dbbb-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="8dbbb-175">La `IAuthenticationManager` interfaz es el punto de entrada principal en el sistema de autenticación 1. x.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="8dbbb-176">Se ha reemplazado por un nuevo conjunto de `HttpContext` métodos de extensión en el `Microsoft.AspNetCore.Authentication` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="8dbbb-177">Por ejemplo, los proyectos de 1. x hacen referencia a una `Authentication` propiedad:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="8dbbb-178">En los proyectos de 2,0, importe el `Microsoft.AspNetCore.Authentication` espacio de nombres y elimine las `Authentication` referencias de propiedad:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="8dbbb-179">Autenticación de Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="8dbbb-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="8dbbb-180">Hay dos variaciones de la autenticación de Windows:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="8dbbb-181">El host solo permite usuarios autenticados.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-181">The host only allows authenticated users.</span></span> <span data-ttu-id="8dbbb-182">Esta variación no se ve afectada por los cambios 2,0.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="8dbbb-183">El host permite usuarios anónimos y autenticados.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="8dbbb-184">Esta variación se ve afectada por los cambios 2,0.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="8dbbb-185">Por ejemplo, la aplicación debe permitir usuarios anónimos en el nivel de [IIS](xref:host-and-deploy/iis/index) o de [HTTP.sys](xref:fundamentals/servers/httpsys) , pero autorizar a los usuarios en el nivel de controlador.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="8dbbb-186">En este escenario, establezca el esquema predeterminado en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="8dbbb-187">Para [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), establezca el esquema predeterminado en `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="8dbbb-188">Para [Microsoft. AspNetCore. Server. https](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), establezca el esquema predeterminado en `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="8dbbb-189">Si no se establece el esquema predeterminado, se impide que la solicitud de autorización (desafío) funcione con la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="8dbbb-190">`System.InvalidOperationException`: No se especificó ningún authenticationScheme y no se encontró ningún DefaultChallengeScheme.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="8dbbb-191">Para obtener más información, vea <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-:::no-loc(cookie):::-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="8dbbb-192">:::no-loc(Identity)::::::no-loc(Cookie):::Instancias de opciones</span><span class="sxs-lookup"><span data-stu-id="8dbbb-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options instances</span></span>

<span data-ttu-id="8dbbb-193">Un efecto secundario de los cambios 2,0 es el cambio al uso de opciones con nombre en lugar de :::no-loc(cookie)::: instancias de opciones.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-193">A side effect of the 2.0 changes is the switch to using named options instead of :::no-loc(cookie)::: options instances.</span></span> <span data-ttu-id="8dbbb-194">Se quita la capacidad de personalizar los :::no-loc(Identity)::: :::no-loc(cookie)::: nombres de esquema.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-194">The ability to customize the :::no-loc(Identity)::: :::no-loc(cookie)::: scheme names is removed.</span></span>

<span data-ttu-id="8dbbb-195">Por ejemplo, los proyectos de 1. x usan la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) para pasar un `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parámetro a *AccountController.CS* y *ManageController.CS* .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="8dbbb-196">:::no-loc(cookie):::Se tiene acceso al esquema de autenticación externo desde la instancia de proporcionada:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-196">The external :::no-loc(cookie)::: authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="8dbbb-197">La inserción de constructores mencionada no es necesaria en los proyectos 2,0 y `_external:::no-loc(Cookie):::Scheme` se puede eliminar el campo:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_external:::no-loc(Cookie):::Scheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="8dbbb-198">los proyectos de 1. x usaron el `_external:::no-loc(Cookie):::Scheme` campo de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-198">1.x projects used the `_external:::no-loc(Cookie):::Scheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="8dbbb-199">En proyectos de 2,0, reemplace el código anterior por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="8dbbb-200">La `:::no-loc(Identity):::Constants.ExternalScheme` constante se puede usar directamente.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-200">The `:::no-loc(Identity):::Constants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="8dbbb-201">Resuelva la llamada agregada recientemente `SignOutAsync` importando el siguiente espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="8dbbb-202">Agregar :::no-loc(Identity)::: propiedades de navegación poco de usuario</span><span class="sxs-lookup"><span data-stu-id="8dbbb-202">Add :::no-loc(Identity):::User POCO navigation properties</span></span>

<span data-ttu-id="8dbbb-203">Se han quitado las propiedades de navegación básicas de Entity Framework (EF) de la base `:::no-loc(Identity):::User` poco (objeto CLR anterior).</span><span class="sxs-lookup"><span data-stu-id="8dbbb-203">The Entity Framework (EF) Core navigation properties of the base `:::no-loc(Identity):::User` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="8dbbb-204">Si el proyecto 1. x usó estas propiedades, agréguelas de nuevo al proyecto 2,0:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserRole<int>> Roles { get; } = new List<:::no-loc(Identity):::UserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserClaim<int>> Claims { get; } = new List<:::no-loc(Identity):::UserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserLogin<int>> Logins { get; } = new List<:::no-loc(Identity):::UserLogin<int>>();
```

<span data-ttu-id="8dbbb-205">Para evitar la duplicación de claves externas al ejecutar EF Core migraciones, agregue lo siguiente al método de la `:::no-loc(Identity):::DbContext` clase `OnModelCreating` (después de la `base.OnModelCreating();` llamada):</span><span class="sxs-lookup"><span data-stu-id="8dbbb-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `:::no-loc(Identity):::DbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
    // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="8dbbb-206">Reemplazar GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="8dbbb-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="8dbbb-207">El método sincrónico `GetExternalAuthenticationSchemes` se quitó en favor de una versión asincrónica.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="8dbbb-208">los proyectos de 1. x tienen el código siguiente en *Controllers/ManageController. CS* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="8dbbb-209">Este método aparece también en *views/Account/login. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="8dbbb-210">En los proyectos de 2,0, use el <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> método.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="8dbbb-211">El cambio en *ManageController.CS* es similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8dbbb-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="8dbbb-212">En *login. cshtml* , la `AuthenticationScheme` propiedad a la que se tiene acceso en el `foreach` bucle cambia a `Name` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="8dbbb-213">Cambio de la propiedad ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="8dbbb-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="8dbbb-214">Un `ManageLoginsViewModel` objeto se usa en la `ManageLogins` acción de *ManageController.CS* .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="8dbbb-215">En los proyectos de 1. x, el `OtherLogins` tipo de valor devuelto de la propiedad del objeto es `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="8dbbb-216">Este tipo de valor devuelto requiere una importación de `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="8dbbb-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="8dbbb-217">En los proyectos de 2,0, el tipo de valor devuelto cambia a `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="8dbbb-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="8dbbb-218">Este nuevo tipo de valor devuelto requiere reemplazar la `Microsoft.AspNetCore.Http.Authentication` importación por una `Microsoft.AspNetCore.Authentication` importación.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="8dbbb-219">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8dbbb-219">Additional resources</span></span>

<span data-ttu-id="8dbbb-220">Para obtener más información, consulte el problema [de la explicación sobre Auth 2,0](https://github.com/aspnet/Security/issues/1338) en github.</span><span class="sxs-lookup"><span data-stu-id="8dbbb-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
