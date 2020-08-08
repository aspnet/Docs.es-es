---
title: Migración de la autenticación y Identity a ASP.NET Core 2,0
author: scottaddie
description: En este artículo se describen los pasos más comunes para migrar la autenticación ASP.NET Core 1. x y Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 46f10df25235b532f188eda2a079aef71070cd6d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015295"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a>Migración de la autenticación y Identity a ASP.NET Core 2,0

Por [Scott Addie](https://github.com/scottaddie) y [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 tiene un nuevo modelo de autenticación y [Identity](xref:security/authentication/identity) que simplifica la configuración mediante servicios. ASP.NET Core 1. x aplicaciones que usan la autenticación de o Identity se pueden actualizar para usar el nuevo modelo, tal y como se describe a continuación.

## <a name="update-namespaces"></a>Actualizar espacios de nombres

En 1. x, se han `IdentityRole` encontrado clases como y `IdentityUser` en el `Microsoft.AspNetCore.Identity.EntityFrameworkCore` espacio de nombres.

En 2,0, el <xref:Microsoft.AspNetCore.Identity> espacio de nombres se convirtió en el nuevo inicio de algunas de estas clases. Con el Identity código predeterminado, las clases afectadas incluyen `ApplicationUser` y `Startup` . Ajuste las `using` instrucciones para resolver las referencias afectadas.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware de autenticación y servicios

En los proyectos de 1. x, la autenticación se configura mediante middleware. Se invoca un método de middleware para cada esquema de autenticación que desee admitir.

En el siguiente ejemplo de 1. x se configura la autenticación Identity de Facebook con en *Startup.CS*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

En los proyectos de 2,0, la autenticación se configura a través de los servicios. Cada esquema de autenticación se registra en el `ConfigureServices` método de *Startup.CS*. El `UseIdentity` método se reemplaza por `UseAuthentication` .

En el siguiente ejemplo de 2,0 se configura la autenticación Identity de Facebook con en *Startup.CS*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
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

El `UseAuthentication` método agrega un componente de middleware de autenticación única, que es responsable de la autenticación automática y el control de las solicitudes de autenticación remota. Reemplaza todos los componentes de middleware individuales por un único componente común de middleware.

A continuación se muestran 2,0 instrucciones de migración para cada esquema de autenticación principal.

### <a name="no-loccookie-based-authentication"></a>Cookieautenticación basada en

Seleccione una de las dos opciones siguientes y realice los cambios necesarios en *Startup.CS*:

1. Usar cookie s conIdentity
    - Reemplace `UseIdentity` por `UseAuthentication` en el `Configure` método:

        ```csharp
        app.UseAuthentication();
        ```

    - Invoque el `AddIdentity` método en el `ConfigureServices` método para agregar los cookie servicios de autenticación.
    - Opcionalmente, invoque `ConfigureApplicationCookie` el `ConfigureExternalCookie` método o en el `ConfigureServices` método para ajustar la Identity cookie configuración.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Usar cookie s sinIdentity
    - Reemplace la `UseCookieAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - Invoque `AddAuthentication` los `AddCookie` métodos y en el `ConfigureServices` método:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Autenticación de portador de JWT

Realice los cambios siguientes en *Startup.CS*:
- Reemplace la `UseJwtBearerAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddJwtBearer` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Este fragmento de código no usa Identity , por lo que el esquema predeterminado se debe establecer pasando `JwtBearerDefaults.AuthenticationScheme` al `AddAuthentication` método.

### <a name="openid-connect-oidc-authentication"></a>Autenticación de OpenID Connect (OIDC)

Realice los cambios siguientes en *Startup.CS*:

- Reemplace la `UseOpenIdConnectAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddOpenIdConnect` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Reemplace la `PostLogoutRedirectUri` propiedad en la `OpenIdConnectOptions` acción por `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Autenticación con Facebook

Realice los cambios siguientes en *Startup.CS*:
- Reemplace la `UseFacebookAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddFacebook` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Autenticación con Google

Realice los cambios siguientes en *Startup.CS*:
- Reemplace la `UseGoogleAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddGoogle` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Autenticación con cuenta Microsoft

Para obtener más información sobre la autenticación de cuenta de Microsoft, consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

Realice los cambios siguientes en *Startup.CS*:
- Reemplace la `UseMicrosoftAccountAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddMicrosoftAccount` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Autenticación con Twitter

Realice los cambios siguientes en *Startup.CS*:
- Reemplace la `UseTwitterAuthentication` llamada al método en el `Configure` método por `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Invoque el `AddTwitter` método en el `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Establecer esquemas de autenticación predeterminados

En 1. x, las `AutomaticAuthenticate` `AutomaticChallenge` propiedades y de la clase base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) se debían establecer en un esquema de autenticación único. No había una buena manera de aplicar esto.

En 2,0, estas dos propiedades se han quitado como propiedades en la `AuthenticationOptions` instancia individual. Se pueden configurar en la `AddAuthentication` llamada al método en el `ConfigureServices` método de *Startup.CS*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

En el fragmento de código anterior, el esquema predeterminado se establece en `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

También puede usar una versión sobrecargada del `AddAuthentication` método para establecer más de una propiedad. En el siguiente ejemplo de método sobrecargado, el esquema predeterminado se establece en `CookieAuthenticationDefaults.AuthenticationScheme` . El esquema de autenticación puede especificarse también en sus `[Authorize]` atributos individuales o en las directivas de autorización.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Defina un esquema predeterminado en 2,0 si se cumple alguna de las siguientes condiciones:
- Quiere que el usuario inicie sesión automáticamente
- El `[Authorize]` atributo o las directivas de autorización se usan sin especificar esquemas.

Una excepción a esta regla es el `AddIdentity` método. Este método agrega cookie s para usted y establece los esquemas de autenticación y desafío predeterminados en la aplicación cookie `IdentityConstants.ApplicationScheme` . Además, establece el esquema de inicio de sesión predeterminado en el externo cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Uso de extensiones de autenticación HttpContext

La `IAuthenticationManager` interfaz es el punto de entrada principal en el sistema de autenticación 1. x. Se ha reemplazado por un nuevo conjunto de `HttpContext` métodos de extensión en el `Microsoft.AspNetCore.Authentication` espacio de nombres.

Por ejemplo, los proyectos de 1. x hacen referencia a una `Authentication` propiedad:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

En los proyectos de 2,0, importe el `Microsoft.AspNetCore.Authentication` espacio de nombres y elimine las `Authentication` referencias de propiedad:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Autenticación de Windows (HTTP.sys/IISIntegration)

Hay dos variaciones de la autenticación de Windows:

* El host solo permite usuarios autenticados. Esta variación no se ve afectada por los cambios 2,0.
* El host permite usuarios anónimos y autenticados. Esta variación se ve afectada por los cambios 2,0. Por ejemplo, la aplicación debe permitir usuarios anónimos en el nivel de [IIS](xref:host-and-deploy/iis/index) o de [HTTP.sys](xref:fundamentals/servers/httpsys) , pero autorizar a los usuarios en el nivel de controlador. En este escenario, establezca el esquema predeterminado en el `Startup.ConfigureServices` método.

  Para [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), establezca el esquema predeterminado en `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Para [Microsoft. AspNetCore. Server. https](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), establezca el esquema predeterminado en `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Si no se establece el esquema predeterminado, se impide que la solicitud de autorización (desafío) funcione con la siguiente excepción:

  > `System.InvalidOperationException`: No se especificó ningún authenticationScheme y no se encontró ningún DefaultChallengeScheme.

Para obtener más información, vea <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a>IdentityCookieInstancias de opciones

Un efecto secundario de los cambios 2,0 es el cambio al uso de opciones con nombre en lugar de cookie instancias de opciones. Se quita la capacidad de personalizar los Identity cookie nombres de esquema.

Por ejemplo, los proyectos de 1. x usan la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) para pasar un `IdentityCookieOptions` parámetro a *AccountController.CS* y *ManageController.CS*. cookieSe tiene acceso al esquema de autenticación externo desde la instancia de proporcionada:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

La inserción de constructores mencionada no es necesaria en los proyectos 2,0 y `_externalCookieScheme` se puede eliminar el campo:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

los proyectos de 1. x usaron el `_externalCookieScheme` campo de la siguiente manera:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

En proyectos de 2,0, reemplace el código anterior por lo siguiente. La `IdentityConstants.ExternalScheme` constante se puede usar directamente.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Resuelva la llamada agregada recientemente `SignOutAsync` importando el siguiente espacio de nombres:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a>Agregar Identity propiedades de navegación poco de usuario

Se han quitado las propiedades de navegación básicas de Entity Framework (EF) de la base `IdentityUser` poco (objeto CLR anterior). Si el proyecto 1. x usó estas propiedades, agréguelas de nuevo al proyecto 2,0:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Para evitar la duplicación de claves externas al ejecutar EF Core migraciones, agregue lo siguiente al método de la `IdentityDbContext` clase `OnModelCreating` (después de la `base.OnModelCreating();` llamada):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
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

## <a name="replace-getexternalauthenticationschemes"></a>Reemplazar GetExternalAuthenticationSchemes

El método sincrónico `GetExternalAuthenticationSchemes` se quitó en favor de una versión asincrónica. los proyectos de 1. x tienen el código siguiente en *Controllers/ManageController. CS*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Este método aparece también en *views/Account/login. cshtml* :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

En los proyectos de 2,0, use el <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> método. El cambio en *ManageController.CS* es similar al código siguiente:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

En *login. cshtml*, la `AuthenticationScheme` propiedad a la que se tiene acceso en el `foreach` bucle cambia a `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Cambio de la propiedad ManageLoginsViewModel

Un `ManageLoginsViewModel` objeto se usa en la `ManageLogins` acción de *ManageController.CS*. En los proyectos de 1. x, el `OtherLogins` tipo de valor devuelto de la propiedad del objeto es `IList<AuthenticationDescription>` . Este tipo de valor devuelto requiere una importación de `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

En los proyectos de 2,0, el tipo de valor devuelto cambia a `IList<AuthenticationScheme>` . Este nuevo tipo de valor devuelto requiere reemplazar la `Microsoft.AspNetCore.Http.Authentication` importación por una `Microsoft.AspNetCore.Authentication` importación.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información, consulte el problema [de la explicación sobre Auth 2,0](https://github.com/aspnet/Security/issues/1338) en github.
