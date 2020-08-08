---
title: Usar cookie autenticación sin ASP.net CoreIdentity
author: rick-anderson
description: Aprenda a usar la cookie autenticación sin ASP.net Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 325bc3dcd48517d93d5f6f2d56e92651c780d759
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021892"
---
# <a name="use-no-loccookie-authentication-without-aspnet-core-no-locidentity"></a>Usar cookie autenticación sin ASP.net CoreIdentity

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión. Sin embargo, cookie se puede usar un proveedor de autenticación basado en sin ASP.net Core Identity . Para obtener más información, vea <xref:security/authentication/identity>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación. Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario. El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* . En un ejemplo real, el usuario se autenticaría en una base de datos.

## <a name="configuration"></a>Configuración

Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. Cookie paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

En el `Startup.ConfigureServices` método, cree los servicios de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación. `AuthenticationScheme`resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme). Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema. Puede proporcionar cualquier valor de cadena que distinga el esquema.

El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie . Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada. Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos. Para obtener más información, vea <xref:security/gdpr#essential-cookies>.

En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` para establecer la `HttpContext.User` propiedad y ejecutar el middleware de autorización para las solicitudes. Llame a `UseAuthentication` los `UseAuthorization` métodos y antes de llamar a `UseEndpoints` :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.

Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>CookieMiddleware de directivas

El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva. Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.

El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2. Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` . Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.

| MinimumSameSitePolicy | Cookie. SameSite | Resultado Cookie . Configuración de SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Crear una autenticacióncookie

Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> . La información del usuario se serializa y se almacena en el cookie . 

Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync`crea un cifrado cookie y lo agrega a la respuesta actual. Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>solo se usa en algunas rutas de acceso específicas de forma predeterminada, por ejemplo, las rutas de acceso de inicio de sesión y de cierre de sesión. Para obtener más información, vea el [ Cookie origen de AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).

El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado. En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación. De lo contrario, se usa el esquema predeterminado.

## <a name="react-to-back-end-changes"></a>Reaccionar a los cambios de back-end

Una vez que cookie se crea un, cookie es el único origen de identidad. Si una cuenta de usuario está deshabilitada en los sistemas de servidor:

* El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .
* El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.

El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad. La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.

Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario. Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido. En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor. Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.

Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:

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

Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :

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

Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método. Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo. Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .

> [!WARNING]
> El enfoque descrito aquí se desencadena en cada solicitud. La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.

## <a name="persistent-no-loccookies"></a>cookiePersistentes

Puede que desee que el se cookie conserve entre las sesiones del explorador. Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar. 

El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador. Se respetan los valores de expiración que se hayan configurado previamente. Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>cookieExpiración absoluta

Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Para crear un persistente cookie , `IsPersistent` también se debe establecer. De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene. Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si se establece.

El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos. Esto omite cualquier configuración de expiración variable previamente configurada.

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

ASP.NET Core Identity es un proveedor de autenticación completo con todas las características para crear y mantener inicios de sesión. Sin embargo, cookie se puede usar un proveedor de autenticación de autenticación basado en sin ASP.net Core Identity . Para obtener más información, vea <xref:security/authentication/identity>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Para fines de demostración en la aplicación de ejemplo, la cuenta de usuario para el usuario hipotético, María Rodriguez, está codificada en la aplicación. Use la dirección de **correo electrónico** `maria.rodriguez@contoso.com` y cualquier contraseña para iniciar sesión en el usuario. El usuario se autentica en el `AuthenticateUser` método del archivo *pages/Account/login. cshtml. CS* . En un ejemplo real, el usuario se autenticaría en una base de datos.

## <a name="configuration"></a>Configuración

Si la aplicación no usa el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), cree una referencia de paquete en el archivo de proyecto para [Microsoft. AspNetCore. Authentication. Cookie paquete de](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

En el `Startup.ConfigureServices` método, cree el servicio de middleware de autenticación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos y:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>se pasa a `AddAuthentication` establece el esquema de autenticación predeterminado para la aplicación. `AuthenticationScheme`resulta útil cuando hay varias instancias de cookie autenticación y se desea [autorizar con un esquema específico](xref:security/authorization/limitingidentitybyscheme). Si `AuthenticationScheme` se establece en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) , se proporciona el valor " Cookie s" para el esquema. Puede proporcionar cualquier valor de cadena que distinga el esquema.

El esquema de autenticación de la aplicación es diferente del esquema de autenticación de la aplicación cookie . Cuando cookie no se proporciona un esquema de autenticación a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , usa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

La cookie propiedad de la autenticación <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece en de `true` forma predeterminada. Se permiten las autenticaciones cookie cuando un visitante del sitio no ha dado su consentimiento a la recopilación de datos. Para obtener más información, vea <xref:security/gdpr#essential-cookies>.

En el `Startup.Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad. Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> clase se utiliza para configurar las opciones del proveedor de autenticación.

Establezca `CookieAuthenticationOptions` en la configuración del servicio para la autenticación en el `Startup.ConfigureServices` método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>CookieMiddleware de directivas

El [ Cookie middleware de directivas](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita las capacidades de la cookie Directiva. Agregar el middleware a la canalización de procesamiento de la aplicación es dependiente del orden &mdash; . solo afecta a los componentes de nivel inferior registrados en la canalización.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> proporcionado al middleware de la Cookie Directiva para controlar las características globales de cookie procesamiento y enlazar los cookie controladores de procesamiento cuando cookie se anexan o eliminan.

El <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor predeterminado es `SameSiteMode.Lax` permitir la autenticación de OAuth2. Para aplicar estrictamente una directiva del mismo sitio de `SameSiteMode.Strict` , establezca `MinimumSameSitePolicy` . Aunque esta configuración interrumpe OAuth2 y otros esquemas de autenticación entre orígenes, eleva el nivel de cookie seguridad para otros tipos de aplicaciones que no se basan en el procesamiento de solicitudes entre orígenes.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

La Cookie configuración de middleware de la Directiva para `MinimumSameSitePolicy` puede afectar a la configuración de `Cookie.SameSite` en `CookieAuthenticationOptions` la configuración de acuerdo con la matriz siguiente.

| MinimumSameSitePolicy | Cookie. SameSite | Resultado Cookie . Configuración de SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Crear una autenticacióncookie

Para crear una cookie información de usuario de mantenimiento, construya un <xref:System.Security.Claims.ClaimsPrincipal> . La información del usuario se serializa y se almacena en el cookie . 

Cree un <xref:System.Security.Claims.ClaimsIdentity> con los <xref:System.Security.Claims.Claim> s necesarios y llame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> al usuario para iniciar sesión:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`crea un cifrado cookie y lo agrega a la respuesta actual. Si `AuthenticationScheme` no se especifica, se usa el esquema predeterminado.

El sistema de [protección de datos](xref:security/data-protection/using-data-protection) de ASP.net Core se usa para el cifrado. En el caso de una aplicación hospedada en varios equipos, equilibrio de carga entre aplicaciones o uso de una granja de servidores Web, [Configure la protección de datos](xref:security/data-protection/configuration/overview) para que use el mismo anillo de claves e identificador de la aplicación.

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie , llame a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") no se usa como esquema (por ejemplo, "Contoso Cookie "), proporcione el esquema que se usa al configurar el proveedor de autenticación. De lo contrario, se usa el esquema predeterminado.

## <a name="react-to-back-end-changes"></a>Reaccionar a los cambios de back-end

Una vez que cookie se crea un, cookie es el único origen de identidad. Si una cuenta de usuario está deshabilitada en los sistemas de servidor:

* El sistema de autenticación de la aplicación cookie continúa procesando las solicitudes en función de la autenticación cookie .
* El usuario permanecerá conectado en la aplicación siempre que la autenticación cookie sea válida.

El <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento se puede utilizar para interceptar e invalidar la validación de la cookie identidad. La validación de cookie en cada solicitud reduce el riesgo de que los usuarios revocados accedan a la aplicación.

Un enfoque de cookie validación se basa en realizar un seguimiento de cuándo cambia la base de datos de usuario. Si no se ha cambiado la base de datos desde que se emitió el usuario, no es cookie necesario volver a autenticar al usuario si cookie todavía es válido. En la aplicación de ejemplo, la base de datos se implementa en `IUserRepository` y almacena un `LastChanged` valor. Cuando se actualiza un usuario en la base de datos, el `LastChanged` valor se establece en la hora actual.

Para invalidar un cookie cuando la base de datos cambie según el `LastChanged` valor, cree el objeto cookie con una `LastChanged` solicitud que contenga el `LastChanged` valor actual de la base de datos:

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

Para implementar una invalidación para el `ValidatePrincipal` evento, escriba un método con la siguiente firma en una clase que derive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

El siguiente es un ejemplo de implementación de `CookieAuthenticationEvents` :

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

Registre la instancia de eventos durante cookie el registro del servicio en el `Startup.ConfigureServices` método. Proporcione un [registro de servicio de ámbito](xref:fundamentals/dependency-injection#service-lifetimes) para la `CustomCookieAuthenticationEvents` clase:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Considere una situación en la que el nombre del usuario se actualice &mdash; una decisión que no afecte a la seguridad de ningún modo. Si desea actualizar de la entidad de seguridad de usuario de una no destructiva, llame `context.ReplacePrincipal` a y establezca la `context.ShouldRenew` propiedad en `true` .

> [!WARNING]
> El enfoque descrito aquí se desencadena en cada solicitud. La validación cookie de la autenticación para todos los usuarios de cada solicitud puede dar lugar a una gran penalización del rendimiento de la aplicación.

## <a name="persistent-no-loccookies"></a>cookiePersistentes

Puede que desee que el se cookie conserve entre las sesiones del explorador. Esta persistencia solo se debe habilitar con el consentimiento explícito del usuario con una casilla "Recordarme" al iniciar sesión o un mecanismo similar. 

El fragmento de código siguiente crea una identidad y la correspondiente cookie que sobrevive a través de los cierres del explorador. Se respetan los valores de expiración que se hayan configurado previamente. Si cookie expira mientras se cierra el explorador, el explorador lo borra cookie una vez que se reinicia.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Se establece `true` en en <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>cookieExpiración absoluta

Se puede establecer una hora de expiración absoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Para crear un persistente cookie , `IsPersistent` también se debe establecer. De lo contrario, cookie se crea con una duración basada en sesión y puede expirar antes o después del vale de autenticación que contiene. Cuando `ExpiresUtc` se establece, reemplaza el valor de la <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opción de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si se establece.

El fragmento de código siguiente crea una identidad y la correspondiente cookie que dura 20 minutos. Esto omite cualquier configuración de expiración variable previamente configurada.

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

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Comprobaciones de roles basadas en directivas](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
