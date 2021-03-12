---
title: Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo establecer notificaciones y tokens adicionales de proveedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 513de6133455e26552b79de0f07cf135e8b36825
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605573"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="80f23-103">Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80f23-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80f23-104">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="80f23-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="80f23-105">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="80f23-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="80f23-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80f23-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80f23-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="80f23-107">Prerequisites</span></span>

<span data-ttu-id="80f23-108">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80f23-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="80f23-109">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="80f23-110">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="80f23-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="80f23-111">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="80f23-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="80f23-112">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="80f23-112">Set the client ID and client secret</span></span>

<span data-ttu-id="80f23-113">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="80f23-114">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="80f23-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="80f23-115">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="80f23-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="80f23-116">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="80f23-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="80f23-117">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="80f23-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="80f23-118">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="80f23-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="80f23-119">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="80f23-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="80f23-120">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="80f23-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="80f23-121">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="80f23-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="80f23-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="80f23-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="80f23-123">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="80f23-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="80f23-124">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="80f23-124">Establish the authentication scope</span></span>

<span data-ttu-id="80f23-125">Especifique la lista de permisos que se van a recuperar del proveedor mediante la especificación de <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="80f23-126">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="80f23-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="80f23-127">Proveedor</span><span class="sxs-lookup"><span data-stu-id="80f23-127">Provider</span></span>  | <span data-ttu-id="80f23-128">Ámbito</span><span class="sxs-lookup"><span data-stu-id="80f23-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="80f23-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="80f23-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="80f23-130">Google</span><span class="sxs-lookup"><span data-stu-id="80f23-130">Google</span></span>    | <span data-ttu-id="80f23-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="80f23-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="80f23-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="80f23-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="80f23-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="80f23-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="80f23-134">En la aplicación de ejemplo, `profile` `email` `openid` el marco de trabajo agrega automáticamente los ámbitos, y cuando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> se llama a en <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="80f23-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="80f23-135">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="80f23-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="80f23-136">En el ejemplo siguiente, `https://www.googleapis.com/auth/user.birthday.read` se agrega el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="80f23-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="80f23-137">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="80f23-137">Map user data keys and create claims</span></span>

<span data-ttu-id="80f23-138">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="80f23-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="80f23-139">Para obtener más información sobre los tipos de notificaciones, vea <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="80f23-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="80f23-140">La aplicación de ejemplo crea `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) a partir de las `locale` `picture` claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="80f23-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="80f23-141">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , <xref:Microsoft.AspNetCore.Identity.IdentityUser> `ApplicationUser` se inicia una sesión de () en la aplicación con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="80f23-142">Durante el proceso de inicio de sesión, el <xref:Microsoft.AspNetCore.Identity.UserManager%601> puede almacenar `ApplicationUser` notificaciones para los datos de usuario disponibles en <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="80f23-143">En la aplicación de ejemplo, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. CS*) establece las `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) para la sesión iniciada `ApplicationUser` , incluida una notificación para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="80f23-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="80f23-144">De forma predeterminada, las notificaciones de un usuario se almacenan en la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="80f23-145">Si la autenticación cookie es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="80f23-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="80f23-146">El explorador detecta que el cookie encabezado es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="80f23-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="80f23-147">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="80f23-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="80f23-148">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="80f23-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="80f23-149">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80f23-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="80f23-150">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el Cookie middleware de autenticación <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="80f23-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="80f23-151">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="80f23-152">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="80f23-152">Save the access token</span></span>

<span data-ttu-id="80f23-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define si los tokens de acceso y de actualización deben almacenarse en <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="80f23-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="80f23-154">`SaveTokens` se establece en de `false` forma predeterminada para reducir el tamaño de la autenticación final cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="80f23-155">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="80f23-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="80f23-156">Cuando se `OnPostConfirmationAsync` ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en el `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="80f23-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="80f23-157">La aplicación de ejemplo guarda el token de acceso en `OnPostConfirmationAsync` (nuevo registro de usuario) y `OnGetCallbackAsync` (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="80f23-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="80f23-158">Para obtener información sobre cómo pasar tokens a los Razor componentes de una Blazor Server aplicación, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="80f23-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="80f23-159">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="80f23-159">How to add additional custom tokens</span></span>

<span data-ttu-id="80f23-160">Para mostrar cómo agregar un token personalizado, que se almacena como parte de `SaveTokens` , la aplicación de ejemplo agrega un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con el actual <xref:System.DateTime> para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="80f23-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="80f23-161">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="80f23-161">Creating and adding claims</span></span>

<span data-ttu-id="80f23-162">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="80f23-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="80f23-163">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="80f23-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="80f23-164">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="80f23-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="80f23-165">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="80f23-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="80f23-166">Adición y actualización de notificaciones de usuario</span><span class="sxs-lookup"><span data-stu-id="80f23-166">Add and update user claims</span></span>

<span data-ttu-id="80f23-167">Las notificaciones se copian de proveedores externos a la base de datos de usuario en el primer registro, no en el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="80f23-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="80f23-168">Si se habilitan notificaciones adicionales en una aplicación después de que un usuario se registre para usar la aplicación, llame a [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) en un usuario para forzar la generación de una nueva autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="80f23-169">En el entorno de desarrollo que trabaja con las cuentas de usuario de prueba, puede simplemente eliminar y volver a crear la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="80f23-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="80f23-170">En el caso de los sistemas de producción, las nuevas notificaciones agregadas a la aplicación se pueden rellenar en cuentas de usuario.</span><span class="sxs-lookup"><span data-stu-id="80f23-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="80f23-171">Después de aplicar el [scaffolding de la `ExternalLogin` Página](xref:security/authentication/scaffold-identity) a la aplicación en `Areas/Pages/Identity/Account/Manage` , agregue el código siguiente a `ExternalLoginModel` en el `ExternalLogin.cshtml.cs` archivo.</span><span class="sxs-lookup"><span data-stu-id="80f23-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="80f23-172">Agregue un diccionario de notificaciones agregadas.</span><span class="sxs-lookup"><span data-stu-id="80f23-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="80f23-173">Use las claves de diccionario para contener los tipos de notificaciones y use los valores para contener un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="80f23-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="80f23-174">Agregue la siguiente línea a la parte superior de la clase.</span><span class="sxs-lookup"><span data-stu-id="80f23-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="80f23-175">En el ejemplo siguiente se da por supuesto que se ha agregado una Claim para la imagen de Google del usuario con una imagen de Headshot genérica como valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="80f23-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="80f23-176">Reemplace el código predeterminado del `OnGetCallbackAsync` método por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="80f23-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="80f23-177">El código recorre en bucle el Diccionario de notificaciones.</span><span class="sxs-lookup"><span data-stu-id="80f23-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="80f23-178">Las notificaciones se agregan (rellenan) o se actualizan para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="80f23-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="80f23-179">Cuando se agregan o actualizan notificaciones, el inicio de sesión de usuario se actualiza mediante el <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , conservando las propiedades de autenticación ( `AuthenticationProperties` ) existentes.</span><span class="sxs-lookup"><span data-stu-id="80f23-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

<span data-ttu-id="80f23-180">Se realiza un enfoque similar cuando las notificaciones cambian mientras un usuario inicia sesión, pero no se requiere un paso de reposición.</span><span class="sxs-lookup"><span data-stu-id="80f23-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="80f23-181">Para actualizar las notificaciones de un usuario, llame a lo siguiente en el usuario:</span><span class="sxs-lookup"><span data-stu-id="80f23-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="80f23-182">[UserManager. ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) en el usuario para las notificaciones almacenadas en la base de datos de identidad.</span><span class="sxs-lookup"><span data-stu-id="80f23-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="80f23-183">[SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) en el usuario para forzar la generación de una nueva autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="80f23-184">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="80f23-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="80f23-185">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones del especificado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la colección.</span><span class="sxs-lookup"><span data-stu-id="80f23-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="80f23-186">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="80f23-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="80f23-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="80f23-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="80f23-188">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="80f23-188">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="80f23-189">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="80f23-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="80f23-190">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="80f23-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="80f23-191">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80f23-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80f23-192">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="80f23-192">Prerequisites</span></span>

<span data-ttu-id="80f23-193">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80f23-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="80f23-194">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="80f23-195">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="80f23-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="80f23-196">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="80f23-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="80f23-197">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="80f23-197">Set the client ID and client secret</span></span>

<span data-ttu-id="80f23-198">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="80f23-199">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="80f23-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="80f23-200">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="80f23-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="80f23-201">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="80f23-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="80f23-202">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="80f23-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="80f23-203">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="80f23-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="80f23-204">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="80f23-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="80f23-205">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="80f23-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="80f23-206">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="80f23-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="80f23-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="80f23-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="80f23-208">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="80f23-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="80f23-209">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="80f23-209">Establish the authentication scope</span></span>

<span data-ttu-id="80f23-210">Especifique la lista de permisos que se van a recuperar del proveedor mediante la especificación de <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="80f23-211">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="80f23-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="80f23-212">Proveedor</span><span class="sxs-lookup"><span data-stu-id="80f23-212">Provider</span></span>  | <span data-ttu-id="80f23-213">Ámbito</span><span class="sxs-lookup"><span data-stu-id="80f23-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="80f23-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="80f23-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="80f23-215">Google</span><span class="sxs-lookup"><span data-stu-id="80f23-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="80f23-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="80f23-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="80f23-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="80f23-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="80f23-218">En la aplicación de ejemplo, `userinfo.profile` el ámbito de Google lo agrega automáticamente el marco de trabajo cuando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> se llama a en <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="80f23-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="80f23-219">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="80f23-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="80f23-220">En el ejemplo siguiente, `https://www.googleapis.com/auth/user.birthday.read` se agrega el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="80f23-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="80f23-221">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="80f23-221">Map user data keys and create claims</span></span>

<span data-ttu-id="80f23-222">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="80f23-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="80f23-223">Para obtener más información sobre los tipos de notificaciones, vea <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="80f23-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="80f23-224">La aplicación de ejemplo crea `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) a partir de las `locale` `picture` claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="80f23-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="80f23-225">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , <xref:Microsoft.AspNetCore.Identity.IdentityUser> `ApplicationUser` se inicia una sesión de () en la aplicación con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="80f23-226">Durante el proceso de inicio de sesión, el <xref:Microsoft.AspNetCore.Identity.UserManager%601> puede almacenar `ApplicationUser` notificaciones para los datos de usuario disponibles en <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="80f23-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="80f23-227">En la aplicación de ejemplo, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. CS*) establece las `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) para la sesión iniciada `ApplicationUser` , incluida una notificación para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="80f23-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="80f23-228">De forma predeterminada, las notificaciones de un usuario se almacenan en la autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="80f23-229">Si la autenticación cookie es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="80f23-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="80f23-230">El explorador detecta que el cookie encabezado es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="80f23-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="80f23-231">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="80f23-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="80f23-232">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="80f23-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="80f23-233">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80f23-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="80f23-234">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el Cookie middleware de autenticación <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="80f23-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="80f23-235">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="80f23-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="80f23-236">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="80f23-236">Save the access token</span></span>

<span data-ttu-id="80f23-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define si los tokens de acceso y de actualización deben almacenarse en <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="80f23-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="80f23-238">`SaveTokens` se establece en de `false` forma predeterminada para reducir el tamaño de la autenticación final cookie .</span><span class="sxs-lookup"><span data-stu-id="80f23-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="80f23-239">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="80f23-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="80f23-240">Cuando se `OnPostConfirmationAsync` ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en el `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="80f23-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="80f23-241">La aplicación de ejemplo guarda el token de acceso en `OnPostConfirmationAsync` (nuevo registro de usuario) y `OnGetCallbackAsync` (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="80f23-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="80f23-242">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="80f23-242">How to add additional custom tokens</span></span>

<span data-ttu-id="80f23-243">Para mostrar cómo agregar un token personalizado, que se almacena como parte de `SaveTokens` , la aplicación de ejemplo agrega un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con el actual <xref:System.DateTime> para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="80f23-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="80f23-244">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="80f23-244">Creating and adding claims</span></span>

<span data-ttu-id="80f23-245">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="80f23-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="80f23-246">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="80f23-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="80f23-247">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="80f23-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="80f23-248">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="80f23-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="80f23-249">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="80f23-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="80f23-250">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones del especificado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la colección.</span><span class="sxs-lookup"><span data-stu-id="80f23-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="80f23-251">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="80f23-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="80f23-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="80f23-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="80f23-253">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="80f23-253">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="80f23-254">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="80f23-254">Additional resources</span></span>

* <span data-ttu-id="80f23-255">[aplicación dotnet/AspNetCore Engineering SocialSample](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): la aplicación de ejemplo vinculada se encuentra en la rama [de ingeniería del repositorio de github dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) `main` .</span><span class="sxs-lookup"><span data-stu-id="80f23-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `main` engineering branch.</span></span> <span data-ttu-id="80f23-256">La `main` rama contiene código bajo desarrollo activo para la próxima versión de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="80f23-256">The `main` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="80f23-257">Para ver una versión de la aplicación de ejemplo para una versión de lanzamiento de ASP.NET Core, use la lista desplegable **bifurcar** para seleccionar una rama de versión (por ejemplo, `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="80f23-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
