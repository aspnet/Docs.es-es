---
title: 'Uso compartido de la autenticación cookie entre aplicaciones ASP.net'
author: rick-anderson
description: 'Obtenga información sobre cómo compartir la autenticación cookie entre ASP.net 4. x y las aplicaciones ASP.net Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059694"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="cc3ac-103">Uso compartido de la autenticación cookie entre aplicaciones ASP.net</span><span class="sxs-lookup"><span data-stu-id="cc3ac-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="cc3ac-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc3ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc3ac-105">Los sitios web a menudo se componen de aplicaciones web individuales que trabajan juntas.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="cc3ac-106">Para proporcionar una experiencia de inicio de sesión único (SSO), las aplicaciones web dentro de un sitio deben compartir la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="cc3ac-107">Para admitir este escenario, la pila de protección de datos permite compartir la cookie autenticación Katana y ASP.net Core cookie vales de autenticación.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="cc3ac-108">En los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="cc3ac-108">In the examples that follow:</span></span>

* <span data-ttu-id="cc3ac-109">El cookie nombre de autenticación se establece en un valor común de `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="cc3ac-110">`AuthenticationType`Se establece en `Identity.Application` explícitamente o de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="cc3ac-111">Se usa un nombre de aplicación común para habilitar el sistema de protección de datos para compartir las claves de protección de datos ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="cc3ac-112">`Identity.Application` se utiliza como esquema de autenticación.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="cc3ac-113">Sea cual sea el esquema que se use, se debe usar de forma coherente *dentro y entre* las aplicaciones compartidas, cookie ya sea como esquema predeterminado o mediante su configuración explícita.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="cc3ac-114">El esquema se usa al cifrar y descifrar cookie , por lo que se debe usar un esquema coherente entre las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="cc3ac-115">Se utiliza una ubicación de almacenamiento de [claves de protección de datos](xref:security/data-protection/implementation/key-management) común.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="cc3ac-116">En ASP.NET Core aplicaciones, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se usa para establecer la ubicación de almacenamiento de la clave.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="cc3ac-117">En .NET Framework aplicaciones, el Cookie middleware de autenticación utiliza una implementación de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="cc3ac-118">`DataProtectionProvider` proporciona servicios de protección de datos para el cifrado y descifrado de los datos de carga de autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="cc3ac-119">La `DataProtectionProvider` instancia está aislada del sistema de protección de datos usado por otras partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="cc3ac-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) acepta un <xref:System.IO.DirectoryInfo> para especificar la ubicación del almacenamiento de la clave de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="cc3ac-121">`DataProtectionProvider` requiere el paquete NuGet [Microsoft. AspNetCore. bioprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="cc3ac-122">En ASP.NET Core aplicaciones 2. x, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="cc3ac-123">En .NET Framework aplicaciones, agregue una referencia de paquete a [Microsoft. AspNetCore. desproteccion. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="cc3ac-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> establece el nombre común de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="cc3ac-125">Compartir autenticación cookie con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cc3ac-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="cc3ac-126">Al usar ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="cc3ac-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cc3ac-127">Las claves de protección de datos y el nombre de la aplicación se deben compartir entre las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="cc3ac-128">Se proporciona una ubicación de almacenamiento de claves común al <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método en los ejemplos siguientes.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="cc3ac-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar un nombre común de aplicación compartida ( `SharedCookieApp` en los ejemplos siguientes).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="cc3ac-130">Para obtener más información, vea <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="cc3ac-131">Use el <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensión para configurar el servicio de protección de datos para cookie s.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="cc3ac-132">El tipo de autenticación predeterminado es `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="cc3ac-133">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cc3ac-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="cc3ac-134">Compartir autenticación cookie s sin ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cc3ac-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="cc3ac-135">Cuando use cookie s directamente sin ASP.NET Core Identity , configure la protección de datos y la autenticación en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc3ac-136">En el ejemplo siguiente, el tipo de autenticación se establece en `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="cc3ac-137">Compartir cookie s entre diferentes rutas de acceso base</span><span class="sxs-lookup"><span data-stu-id="cc3ac-137">Share cookies across different base paths</span></span>

<span data-ttu-id="cc3ac-138">Una autenticación cookie utiliza [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como valor predeterminado [ Cookie . Ruta de acceso](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="cc3ac-139">Si la aplicación cookie debe compartirse entre diferentes rutas de acceso base, `Path` se debe invalidar:</span><span class="sxs-lookup"><span data-stu-id="cc3ac-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="cc3ac-140">Uso compartido cookie en subdominios</span><span class="sxs-lookup"><span data-stu-id="cc3ac-140">Share cookies across subdomains</span></span>

<span data-ttu-id="cc3ac-141">Al hospedar aplicaciones que comparten cookie en subdominios, especifique un dominio común en el [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propiedad de dominio.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="cc3ac-142">Para compartir cookie s entre aplicaciones en `contoso.com` , como `first_subdomain.contoso.com` y `second_subdomain.contoso.com` , especifique `Cookie.Domain` como `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="cc3ac-143">Cifrado de claves de protección de datos en reposo</span><span class="sxs-lookup"><span data-stu-id="cc3ac-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="cc3ac-144">En el caso de las implementaciones de producción, configure el `DataProtectionProvider` para cifrar las claves en reposo con DPAPI o un X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="cc3ac-145">Para obtener más información, vea <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="cc3ac-146">En el ejemplo siguiente, se proporciona una huella digital de certificado para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="cc3ac-147">Compartir la autenticación cookie de s entre ASP.net 4. x y las aplicaciones ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cc3ac-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="cc3ac-148">Las aplicaciones ASP.NET 4. x que usan Cookie el middleware de autenticación Katana se pueden configurar para generar las autenticaciones cookie que son compatibles con el Cookie middleware de autenticación ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="cc3ac-149">Esto permite actualizar las aplicaciones individuales de un sitio de gran tamaño en varios pasos, a la vez que proporciona una experiencia de SSO sin problemas en todo el sitio.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="cc3ac-150">Cuando una aplicación usa Cookie el middleware de autenticación Katana, llama a `UseCookieAuthentication` en el archivo *Startup.auth.CS* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="cc3ac-151">Los proyectos de aplicación Web de ASP.NET 4. x creados con Visual Studio 2013 y versiones posteriores usan el middleware de autenticación de Katana Cookie de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="cc3ac-152">Aunque `UseCookieAuthentication` está obsoleto y no se admite para las aplicaciones de ASP.net Core, la llamada a `UseCookieAuthentication` en una aplicación ASP.net 4. x que usa el Cookie middleware de autenticación Katana es válida.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="cc3ac-153">Una aplicación ASP.NET 4. x debe tener como destino .NET Framework 4.5.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="cc3ac-154">De lo contrario, no se pueden instalar los paquetes de NuGet necesarios.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="cc3ac-155">Para compartir la autenticación en cookie una aplicación de ASP.net 4. x y una aplicación ASP.net Core, configure la aplicación de ASP.net Core como se indica en la sección [compartir la autenticación cookie s entre ASP.net Core aplicaciones](#share-authentication-cookies-with-aspnet-core-identity) y, a continuación, configure la aplicación ASP.net 4. x como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="cc3ac-156">Confirme que los paquetes de la aplicación se actualizan a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="cc3ac-157">Instale el paquete [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) en cada aplicación ASP.net 4. x.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="cc3ac-158">Busque y modifique la llamada a `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="cc3ac-159">Cambie el cookie nombre para que coincida con el nombre usado por el Cookie middleware de autenticación de ASP.net Core ( `.AspNet.SharedCookie` en el ejemplo).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="cc3ac-160">En el ejemplo siguiente, el tipo de autenticación se establece en `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="cc3ac-161">Proporcione una instancia de una `DataProtectionProvider` inicializada en la ubicación de almacenamiento de la clave de protección de datos común.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="cc3ac-162">Confirme que el nombre de la aplicación está establecido en el nombre común de la aplicación que usan todas las aplicaciones que comparten cookie la autenticación ( `SharedCookieApp` en el ejemplo).</span><span class="sxs-lookup"><span data-stu-id="cc3ac-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="cc3ac-163">Si no `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` se establece y `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> se establece en una demanda que distingue a usuarios únicos.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="cc3ac-164">*App_Start/startup.auth.CS* :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-164">*App_Start/Startup.Auth.cs* :</span></span>

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

<span data-ttu-id="cc3ac-165">Al generar una identidad de usuario, el tipo de autenticación ( `Identity.Application` ) debe coincidir con el tipo definido en `AuthenticationType` establecido con `UseCookieAuthentication` en *App_Start/startup.auth.CS* .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="cc3ac-166">*Modelos/ Identity Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="cc3ac-166">*Models/IdentityModels.cs* :</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="cc3ac-167">Usar una base de datos de usuario común</span><span class="sxs-lookup"><span data-stu-id="cc3ac-167">Use a common user database</span></span>

<span data-ttu-id="cc3ac-168">Cuando las aplicaciones usen el mismo Identity esquema (la misma versión de Identity ), confirme que el Identity sistema de cada aplicación señala a la misma base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="cc3ac-169">De lo contrario, el sistema de identidad produce errores en tiempo de ejecución cuando intenta hacer coincidir la información de la autenticación cookie con la información de su base de datos.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="cc3ac-170">Cuando el Identity esquema es diferente entre las aplicaciones, normalmente porque las aplicaciones usan Identity versiones diferentes, compartir una base de datos común basada en la versión más reciente de Identity no es posible sin volver a asignar y Agregar columnas en los esquemas de otra aplicación Identity .</span><span class="sxs-lookup"><span data-stu-id="cc3ac-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="cc3ac-171">A menudo es más eficaz actualizar las otras aplicaciones para que usen la versión más reciente para Identity que las aplicaciones puedan compartir una base de datos común.</span><span class="sxs-lookup"><span data-stu-id="cc3ac-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc3ac-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cc3ac-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
