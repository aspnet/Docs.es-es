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
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053129"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorizar con un esquema específico en ASP.NET Core

En algunos escenarios, como las aplicaciones de una sola página (Spa), es habitual usar varios métodos de autenticación. Por ejemplo, la aplicación puede usar la cookie autenticación basada en para iniciar sesión y la autenticación de portador de JWT para solicitudes de JavaScript. En algunos casos, la aplicación puede tener varias instancias de un controlador de autenticación. Por ejemplo, dos cookie Controladores donde uno contiene una identidad básica y uno se crea cuando se desencadena una autenticación multifactor (MFA). MFA se puede desencadenar porque el usuario solicitó una operación que requiere seguridad adicional. Para obtener más información sobre cómo aplicar MFA cuando un usuario solicita un recurso que requiere MFA, consulte la sección de protección de problemas de GitHub [con MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).

Un esquema de autenticación se denomina cuando el servicio de autenticación se configura durante la autenticación. Por ejemplo:

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

En el código anterior, se han agregado dos controladores de autenticación: uno para cookie s y otro para el portador.

>[!NOTE]
>Al especificar el esquema predeterminado, `HttpContext.User` se establece la propiedad en esa identidad. Si no se desea ese comportamiento, deshabilítelo mediante la invocación del formulario sin parámetros de `AddAuthentication` .

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Seleccionar el esquema con el atributo Authorize

En el momento de la autorización, la aplicación indica el controlador que se va a usar. Seleccione el controlador con el que se autorizará la aplicación pasando una lista delimitada por comas de esquemas de autenticación a `[Authorize]` . El `[Authorize]` atributo especifica el esquema de autenticación o los esquemas que se van a usar independientemente de si se ha configurado un valor predeterminado. Por ejemplo:

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

En el ejemplo anterior, los cookie controladores de portador y se ejecutan y tienen la oportunidad de crear y anexar una identidad para el usuario actual. Al especificar solo un esquema, se ejecuta el controlador correspondiente.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

En el código anterior, solo se ejecuta el controlador con el esquema "portador". cookieSe omiten las identidades basadas en.

## <a name="selecting-the-scheme-with-policies"></a>Seleccionar el esquema con directivas

Si prefiere especificar los esquemas deseados en la [Directiva](xref:security/authorization/policies), puede establecer la `AuthenticationSchemes` colección al agregar la Directiva:

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

En el ejemplo anterior, la Directiva "Over18" solo se ejecuta con la identidad creada por el controlador de "portador". Use la Directiva estableciendo la `[Authorize]` propiedad del atributo `Policy` :

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Usar varios esquemas de autenticación

Es posible que algunas aplicaciones necesiten admitir varios tipos de autenticación. Por ejemplo, es posible que la aplicación autentique a los usuarios de Azure Active Directory y de una base de datos de usuarios. Otro ejemplo es una aplicación que autentica a los usuarios de Servicios de federación de Active Directory (AD FS) y Azure Active Directory B2C. En este caso, la aplicación debe aceptar un token de portador JWT de varios emisores.

Agregue todos los esquemas de autenticación que desea aceptar. Por ejemplo, el código siguiente en `Startup.ConfigureServices` agrega dos esquemas de autenticación de portador JWT con distintos emisores:

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
> Solo se registra una autenticación de portador de JWT con el esquema de autenticación predeterminado `JwtBearerDefaults.AuthenticationScheme` . La autenticación adicional tiene que registrarse con un esquema de autenticación único.

El siguiente paso consiste en actualizar la Directiva de autorización predeterminada para aceptar ambos esquemas de autenticación. Por ejemplo:

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

Como la Directiva de autorización predeterminada se invalida, es posible usar el `[Authorize]` atributo en los controladores. Después, el controlador acepta las solicitudes con JWT emitidos por el primer o el segundo emisor.

::: moniker-end
