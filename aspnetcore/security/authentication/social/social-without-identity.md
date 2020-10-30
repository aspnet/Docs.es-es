---
title: La autenticación de Facebook, Google y proveedores externos sin ASP.NET Core Identity
author: rick-anderson
description: Una explicación del uso de Facebook, Google, Twitter, etc. autenticación de usuarios de cuentas sin ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060292"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>Usar autenticación de proveedor de inicio de sesión social sin ASP.NET Core Identity

Por [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos. El enfoque descrito en dicho tema incluye ASP.NET Core Identity como proveedor de autenticación.

Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.NET Core Identity . Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core Identity , pero que aún requieren la integración con un proveedor de autenticación externo de confianza.

Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios. El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google. Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuración

En el `ConfigureServices` método, configure los esquemas de autenticación de la aplicación con los <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos, y <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

La llamada a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> establece el de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`Es el esquema predeterminado que usan los siguientes `HttpContext` métodos de extensión de autenticación:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Al establecer el valor de la aplicación `DefaultScheme` en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), se configura la aplicación para que use Cookie s como esquema predeterminado para estos métodos de extensión. Al establecer el valor de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync` . `DefaultChallengeScheme` invalida `DefaultScheme` . Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para ver las propiedades adicionales que invalidan `DefaultScheme` cuando se establece.

En `Startup.Configure` , llame a `UseAuthentication` y `UseAuthorization` entre las llamadas a `UseRouting` y `UseEndpoints` . Esto establece la `HttpContext.User` propiedad y ejecuta el middleware de autorización para las solicitudes:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts). Para obtener más información acerca de la cookie autenticación, vea <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Aplicar autorización

Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, una acción o una página. El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie , llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). En el código siguiente se agrega un `Logout` controlador de página a la página de *Índice* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que la llamada a no `SignOutAsync` especifica un esquema de autenticación. La de la aplicación `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> describe cómo permitir a los usuarios iniciar sesión con OAuth 2,0 con las credenciales de proveedores de autenticación externos. El enfoque descrito en dicho tema incluye ASP.NET Core Identity como proveedor de autenticación.

Este ejemplo muestra cómo utilizar un proveedor de autenticación externo **sin** ASP.NET Core Identity . Esto resulta útil para las aplicaciones que no requieren todas las características de ASP.NET Core Identity , pero que aún requieren la integración con un proveedor de autenticación externo de confianza.

Este ejemplo utiliza la [autenticación de Google](xref:security/authentication/google-logins) para autenticar a los usuarios. El uso de la autenticación de Google desplaza muchas de las complejidades de la administración del proceso de inicio de sesión en Google. Para integrar con otro proveedor de autenticación externo, vea los temas siguientes:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuración

En el `ConfigureServices` método, configure los esquemas de autenticación de la aplicación con los `AddAuthentication` `AddCookie` métodos, y `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

La llamada a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) establece el [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de la aplicación. `DefaultScheme`Es el esquema predeterminado que usan los siguientes `HttpContext` métodos de extensión de autenticación:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Al establecer el valor de la aplicación `DefaultScheme` en [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), se configura la aplicación para que use Cookie s como esquema predeterminado para estos métodos de extensión. Al establecer el valor de la aplicación <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> en [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), se configura la aplicación para usar Google como esquema predeterminado para las llamadas a `ChallengeAsync` . `DefaultChallengeScheme` invalida `DefaultScheme` . Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para ver las propiedades adicionales que invalidan `DefaultScheme` cuando se establece.

En el `Configure` método, llame al `UseAuthentication` método para invocar el middleware de autenticación que establece la `HttpContext.User` propiedad. Llame al `UseAuthentication` método antes de llamar a `UseMvcWithDefaultRoute` o a `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Para obtener más información sobre los esquemas de autenticación, vea [conceptos de autenticación](xref:security/authentication/index#authentication-concepts). Para obtener más información acerca de la cookie autenticación, vea <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Aplicar autorización

Pruebe la configuración de autenticación de la aplicación aplicando el `AuthorizeAttribute` atributo a un controlador, una acción o una página. El código siguiente limita el acceso a la página de *privacidad* a los usuarios que se han autenticado:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Cerrar sesión

Para cerrar la sesión del usuario actual y eliminar su cookie , llame a [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). En el código siguiente se agrega un `Logout` controlador de página a la página de *Índice* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que la llamada a no `SignOutAsync` especifica un esquema de autenticación. La de la aplicación `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` se usa como retroceso.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
