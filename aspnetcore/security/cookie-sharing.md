---
title: Uso compartido de la autenticación cookie entre aplicaciones ASP.net
author: rick-anderson
description: Obtenga información sobre cómo compartir la autenticación cookie entre ASP.net 4. x y las aplicaciones ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 6ac808d11790ae27e82606b442ff215d95b93e41
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631373"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Uso compartido de la autenticación cookie entre aplicaciones ASP.net

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Los sitios web a menudo se componen de aplicaciones web individuales que trabajan juntas. Para proporcionar una experiencia de inicio de sesión único (SSO), las aplicaciones web dentro de un sitio deben compartir la autenticación cookie . Para admitir este escenario, la pila de protección de datos permite compartir la cookie autenticación Katana y ASP.net Core cookie vales de autenticación.

En los ejemplos siguientes:

* El cookie nombre de autenticación se establece en un valor común de `.AspNet.SharedCookie` .
* `AuthenticationType`Se establece en `Identity.Application` explícitamente o de forma predeterminada.
* Se usa un nombre de aplicación común para habilitar el sistema de protección de datos para compartir las claves de protección de datos ( `SharedCookieApp` ).
* `Identity.Application` se utiliza como esquema de autenticación. Sea cual sea el esquema que se use, se debe usar de forma coherente *dentro y entre* las aplicaciones compartidas, cookie ya sea como esquema predeterminado o mediante su configuración explícita. El esquema se usa al cifrar y descifrar cookie , por lo que se debe usar un esquema coherente entre las aplicaciones.
* Se utiliza una ubicación de almacenamiento de [claves de protección de datos](xref:security/data-protection/implementation/key-management) común.
  * En ASP.NET Core aplicaciones, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se usa para establecer la ubicación de almacenamiento de la clave.
  * En .NET Framework aplicaciones, el Cookie middleware de autenticación utiliza una implementación de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` proporciona servicios de protección de datos para el cifrado y descifrado de los datos de carga de autenticación cookie . La `DataProtectionProvider` instancia está aislada del sistema de protección de datos usado por otras partes de la aplicación. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) acepta un <xref:System.IO.DirectoryInfo> para especificar la ubicación del almacenamiento de la clave de protección de datos.
* `DataProtectionProvider` requiere el paquete NuGet [Microsoft. AspNetCore. bioprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * En ASP.NET Core aplicaciones 2. x, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * En .NET Framework aplicaciones, agregue una referencia de paquete a [Microsoft. AspNetCore. desproteccion. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> establece el nombre común de la aplicación.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Compartir autenticación cookie con ASP.NET Core Identity

Al usar ASP.NET Core Identity:

* Las claves de protección de datos y el nombre de la aplicación se deben compartir entre las aplicaciones. Se proporciona una ubicación de almacenamiento de claves común al <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método en los ejemplos siguientes. Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar un nombre común de aplicación compartida ( `SharedCookieApp` en los ejemplos siguientes). Para más información, consulte <xref:security/data-protection/configuration/overview>.
* Use el <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensión para configurar el servicio de protección de datos para cookie s.
* El tipo de autenticación predeterminado es `Identity.Application` .

En `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Compartir autenticación cookie s sin ASP.NET Core Identity

Cuando use cookie s directamente sin ASP.NET Core Identity , configure la protección de datos y la autenticación en `Startup.ConfigureServices` . En el ejemplo siguiente, el tipo de autenticación se establece en `Identity.Application` :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>Compartir cookie s entre diferentes rutas de acceso base

Una autenticación cookie utiliza [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como valor predeterminado [ Cookie . Ruta de acceso](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Si la aplicación cookie debe compartirse entre diferentes rutas de acceso base, `Path` se debe invalidar:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Uso compartido cookie en subdominios

Al hospedar aplicaciones que comparten cookie en subdominios, especifique un dominio común en el [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propiedad de dominio. Para compartir cookie s entre aplicaciones en `contoso.com` , como `first_subdomain.contoso.com` y `second_subdomain.contoso.com` , especifique `Cookie.Domain` como `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Cifrado de claves de protección de datos en reposo

En el caso de las implementaciones de producción, configure el `DataProtectionProvider` para cifrar las claves en reposo con DPAPI o un X509Certificate. Para más información, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>. En el ejemplo siguiente, se proporciona una huella digital de certificado para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Compartir la autenticación cookie de s entre ASP.net 4. x y las aplicaciones ASP.net Core

Las aplicaciones ASP.NET 4. x que usan Cookie el middleware de autenticación Katana se pueden configurar para generar las autenticaciones cookie que son compatibles con el Cookie middleware de autenticación ASP.net Core. Esto permite actualizar las aplicaciones individuales de un sitio de gran tamaño en varios pasos, a la vez que proporciona una experiencia de SSO sin problemas en todo el sitio.

Cuando una aplicación usa Cookie el middleware de autenticación Katana, llama a `UseCookieAuthentication` en el archivo *Startup.auth.CS* del proyecto. Los proyectos de aplicación Web de ASP.NET 4. x creados con Visual Studio 2013 y versiones posteriores usan el middleware de autenticación de Katana Cookie de forma predeterminada. Aunque `UseCookieAuthentication` está obsoleto y no se admite para las aplicaciones de ASP.net Core, la llamada a `UseCookieAuthentication` en una aplicación ASP.net 4. x que usa el Cookie middleware de autenticación Katana es válida.

Una aplicación ASP.NET 4. x debe tener como destino .NET Framework 4.5.1 o posterior. De lo contrario, no se pueden instalar los paquetes de NuGet necesarios.

Para compartir la autenticación en cookie una aplicación de ASP.net 4. x y una aplicación ASP.net Core, configure la aplicación de ASP.net Core como se indica en la sección [compartir la autenticación cookie s entre ASP.net Core aplicaciones](#share-authentication-cookies-with-aspnet-core-identity) y, a continuación, configure la aplicación ASP.net 4. x como se indica a continuación.

Confirme que los paquetes de la aplicación se actualizan a las versiones más recientes. Instale el paquete [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) en cada aplicación ASP.net 4. x.

Busque y modifique la llamada a `UseCookieAuthentication` :

* Cambie el cookie nombre para que coincida con el nombre usado por el Cookie middleware de autenticación de ASP.net Core ( `.AspNet.SharedCookie` en el ejemplo).
* En el ejemplo siguiente, el tipo de autenticación se establece en `Identity.Application` .
* Proporcione una instancia de una `DataProtectionProvider` inicializada en la ubicación de almacenamiento de la clave de protección de datos común.
* Confirme que el nombre de la aplicación está establecido en el nombre común de la aplicación que usan todas las aplicaciones que comparten cookie la autenticación ( `SharedCookieApp` en el ejemplo).

Si no `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` se establece y `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> se establece en una demanda que distingue a usuarios únicos.

*App_Start/startup.auth.CS*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Al generar una identidad de usuario, el tipo de autenticación ( `Identity.Application` ) debe coincidir con el tipo definido en `AuthenticationType` establecido con `UseCookieAuthentication` en *App_Start/startup.auth.CS*.

*Modelos/ Identity Models.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Usar una base de datos de usuario común

Cuando las aplicaciones usen el mismo Identity esquema (la misma versión de Identity ), confirme que el Identity sistema de cada aplicación señala a la misma base de datos de usuario. De lo contrario, el sistema de identidad produce errores en tiempo de ejecución cuando intenta hacer coincidir la información de la autenticación cookie con la información de su base de datos.

Cuando el Identity esquema es diferente entre las aplicaciones, normalmente porque las aplicaciones usan Identity versiones diferentes, compartir una base de datos común basada en la versión más reciente de Identity no es posible sin volver a asignar y Agregar columnas en los esquemas de otra aplicación Identity . A menudo es más eficaz actualizar las otras aplicaciones para que usen la versión más reciente para Identity que las aplicaciones puedan compartir una base de datos común.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:host-and-deploy/web-farm>
