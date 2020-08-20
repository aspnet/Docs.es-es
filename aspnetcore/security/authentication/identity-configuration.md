---
title: Configuración de ASP.NET Core Identity
author: AdrienTorris
description: Comprenda ASP.NET Core Identity los valores predeterminados y aprenda a configurar Identity las propiedades para usar valores personalizados.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
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
uid: security/authentication/identity-configuration
ms.openlocfilehash: ae4a2eb9d95339651c3810a9f8489d703d73a3fe
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632686"
---
# <a name="configure-no-locaspnet-core-identity"></a>Configuración de ASP.NET Core Identity

ASP.NET Core Identity usa valores predeterminados para la configuración, como la Directiva de contraseñas, el bloqueo y la cookie configuración. Esta configuración se puede invalidar en la `Startup` clase.

## <a name="no-locidentity-options"></a>Identity Opciones

La clase [ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) representa las opciones que se pueden utilizar para configurar el Identity sistema. `IdentityOptions` debe establecerse **después** de llamar a `AddIdentity` o `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>Surja Identity

[ Identity Options. Identity Claims](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) especifica las [ Identity Opciones de notificaciones](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) con las propiedades que se muestran en la tabla siguiente.

| Propiedad | Descripción | Valor predeterminado |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Obtiene o establece el tipo de demanda que se usa para una demanda de rol. | [Argumentos ClaimType. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Obtiene o establece el tipo de notificaciones que se usa para la notificaciones de marca de seguridad. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Obtiene o establece el tipo de demanda que se usa para la demanda de identificador de usuario. | [Argumentos ClaimType. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Obtiene o establece el tipo de demanda que se usa para la demanda del nombre de usuario. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Bloqueo

El bloqueo se establece en el método [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

El código anterior se basa en la `Login` Identity plantilla. 

Las opciones de bloqueo se establecen en `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

El código anterior establece las [ Identity Opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) con los valores predeterminados.

Una autenticación correcta restablece el número de intentos de acceso incorrectos y restablece el reloj.

[ Identity Options. bloquear](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) especifica el [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) con las propiedades que se muestran en la tabla.

| Propiedad | Descripción | Valor predeterminado |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Determina si un nuevo usuario puede bloquearse. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | La cantidad de tiempo que un usuario se bloquea cuando se produce un bloqueo. | 5 minutos |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | El número de intentos de acceso incorrectos hasta que se bloquea un usuario, si está habilitado el bloqueo. | 5 |

### <a name="password"></a>Contraseña

De forma predeterminada, Identity requiere que las contraseñas contengan caracteres en mayúsculas, caracteres en minúsculas, dígitos y caracteres no alfanuméricos. Las contraseñas deben tener al menos seis caracteres.

Las contraseñas se configuran con:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions> en `Startup.ConfigureServices`.
* [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) de `Password` las propiedades si Identity se [scaffolding en la aplicación](xref:security/authentication/scaffold-identity). `InputModel`las `Password` propiedades se encuentran en los siguientes archivos:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) especifica el [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) con las propiedades que se muestran en la tabla.

| Propiedad | Descripción | Valor predeterminado |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Requiere un número entre 0-9 en la contraseña. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | La longitud mínima de la contraseña. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Requiere un carácter en minúscula en la contraseña. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Requiere un carácter no alfanumérico en la contraseña. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Solo se aplica a ASP.NET Core 2,0 o posterior.<br><br> Requiere el número de caracteres distintos en la contraseña. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Requiere un carácter en mayúsculas en la contraseña. | `true` |

### <a name="sign-in"></a>Inicio de sesión

En el código siguiente `SignIn` se establece la configuración (en los valores predeterminados):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. Signen](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) especifica el [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) con las propiedades que se muestran en la tabla.

| Propiedad | Descripción | Valor predeterminado |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Requiere un correo electrónico confirmado para iniciar sesión. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Requiere un número de teléfono confirmado para iniciar sesión. | `false` |

### <a name="tokens"></a>Tokens

[ Identity Options. tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) especifica el [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) con las propiedades que se muestran en la tabla.

| Propiedad | Descripción |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Obtiene o establece el `AuthenticatorTokenProvider` utilizado para validar los inicios de sesión en dos fases con un autenticador. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Obtiene o establece el `ChangeEmailTokenProvider` que se usa para generar tokens usados en correos electrónicos de confirmación de cambio de correo electrónico. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Obtiene o establece el `ChangePhoneNumberTokenProvider` utilizado para generar los tokens que se usan al cambiar los números de teléfono. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Obtiene o establece el proveedor de tokens que se usa para generar los tokens que se usan en los correos electrónicos de confirmación de la cuenta. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Obtiene o establece el [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) que se usa para generar los tokens que se usan en los correos electrónicos de restablecimiento de contraseña. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Se utiliza para construir un [proveedor de tokens de usuario](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) con la clave utilizada como nombre del proveedor. |

### <a name="user"></a>Usuario

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) especifica el [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) con las propiedades que se muestran en la tabla.

| Propiedad | Descripción | Valor predeterminado |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Caracteres permitidos en el nombre de usuario. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Requiere que cada usuario tenga un correo electrónico único. | `false` |

### <a name="no-loccookie-settings"></a>Configuración deCookie

Configure la aplicación cookie en `Startup.ConfigureServices` . [ConfigureApplication Cookie ](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) se debe llamar **después** de llamar a `AddIdentity` o `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Para obtener más información, vea [ Cookie AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opciones de hash de contraseña

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Obtiene y establece las opciones para el hash de contraseña.

| Opción | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | El modo de compatibilidad utilizado al aplicar el algoritmo hash a nuevas contraseñas. Tiene como valor predeterminado <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. El primer byte de una contraseña con hash, denominada *marcador de formato*, especifica la versión del algoritmo hash que se usa para aplicar un algoritmo hash a la contraseña. Al comprobar una contraseña en un hash, el <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> método selecciona el algoritmo correcto en función del primer byte. Un cliente puede autenticarse independientemente de la versión del algoritmo que se usó para aplicar un algoritmo hash a la contraseña. Establecer el modo de compatibilidad afecta a la operación hash de *nuevas contraseñas*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Número de iteraciones utilizadas al aplicar un algoritmo hash a las contraseñas mediante PBKDF2. Este valor solo se utiliza cuando <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> se establece en <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . El valor debe ser un entero positivo y su valor predeterminado es `10000` . |

En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> se establece `12000` en en `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Requerir globalmente la autenticación de todos los usuarios

[!INCLUDE[](~/includes/requireAuth.md)]