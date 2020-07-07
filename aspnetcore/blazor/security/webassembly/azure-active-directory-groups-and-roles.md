---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402161"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="e6e7e-103">Grupos de Azure AD, roles administrativos y roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="e6e7e-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="e6e7e-104">Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e6e7e-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="e6e7e-105">Azure Active Directory (AAD) proporciona diversos métodos de autorización que se pueden combinar con Identity de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="e6e7e-106">Grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="e6e7e-106">User-defined groups</span></span>
  * <span data-ttu-id="e6e7e-107">Seguridad</span><span class="sxs-lookup"><span data-stu-id="e6e7e-107">Security</span></span>
  * <span data-ttu-id="e6e7e-108">O365</span><span class="sxs-lookup"><span data-stu-id="e6e7e-108">O365</span></span>
  * <span data-ttu-id="e6e7e-109">Distribución</span><span class="sxs-lookup"><span data-stu-id="e6e7e-109">Distribution</span></span>
* <span data-ttu-id="e6e7e-110">Roles</span><span class="sxs-lookup"><span data-stu-id="e6e7e-110">Roles</span></span>
  * <span data-ttu-id="e6e7e-111">Roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="e6e7e-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="e6e7e-112">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="e6e7e-112">User-defined roles</span></span>

<span data-ttu-id="e6e7e-113">Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="e6e7e-114">Independiente con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="e6e7e-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="e6e7e-115">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="e6e7e-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="e6e7e-116">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="e6e7e-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="e6e7e-117">Grupos definidos por el usuario y roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="e6e7e-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="e6e7e-118">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia de tipo `groups`, vea los siguientes artículos sobre Azure.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="e6e7e-119">Asigne usuarios a grupos de AAD definidos por el usuario y a roles administrativos integrados.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="e6e7e-120">Roles que usan grupos de seguridad de Azure AD</span><span class="sxs-lookup"><span data-stu-id="e6e7e-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="e6e7e-121">Atributo `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="e6e7e-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="e6e7e-122">En los siguientes ejemplos se da por hecho que un usuario está asignado al rol integrado de AAD *Administrador de facturación*.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="e6e7e-123">La notificación `groups` única enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="e6e7e-124">La aplicación debe convertir esta matriz JSON de grupos y roles en notificaciones `group` individuales, a partir de las cuales la aplicación podrá crear [directivas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="e6e7e-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="e6e7e-125">Extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz de grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="e6e7e-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-126">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="e6e7e-127">Cree una fábrica de usuario personalizada en la aplicación independiente o la aplicación cliente de una solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="e6e7e-128">La siguiente fábrica también está configurada para controlar matrices de notificaciones `roles`, descritas en la sección [Roles definidos por el usuario](#user-defined-roles):</span><span class="sxs-lookup"><span data-stu-id="e6e7e-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="e6e7e-129">No es necesario incluir código para quitar la notificación `groups` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="e6e7e-130">Registre la fábrica en el archivo `Program.Main` (`Program.cs`) de la aplicación independiente o la aplicación cliente de una solución hospedada:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="e6e7e-131">Cree una [directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="e6e7e-132">En el siguiente ejemplo se crea una directiva para el rol integrado *Administrador de facturación* de AAD:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="e6e7e-133">Para obtener una lista completa de los identificadores de objeto de rol de AAD, vea la sección [Identificadores de grupos de roles de AAD](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="e6e7e-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="e6e7e-134">En los siguientes ejemplos, la aplicación usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="e6e7e-135">El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="e6e7e-136">El acceso a un componente completo se puede basar en la directiva usando la [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="e6e7e-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="e6e7e-137">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="e6e7e-138">También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="e6e7e-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="e6e7e-139">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="e6e7e-139">User-defined roles</span></span>

<span data-ttu-id="e6e7e-140">Una aplicación registrada en AAD también se puede configurar para usar roles definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="e6e7e-141">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia `roles`, vea [Procedimiento para agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="e6e7e-142">En el siguiente ejemplo se da por hecho que una aplicación está configurada con dos roles:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="e6e7e-143">Aunque no se pueden asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, sí se pueden asignar usuarios a roles y recibir una notificación `roles` relativa a los usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="e6e7e-144">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="e6e7e-145">Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="e6e7e-146">La notificación `roles` única enviada por AAD presenta los roles definidos por el usuario como valores `value` de `appRoles` en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="e6e7e-147">La aplicación debe convertir esta matriz JSON de roles en notificaciones `role` individuales.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="e6e7e-148">Los elementos `CustomUserFactory` que se muestran en la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles) están configurados para actuar a partir de una notificación `roles` con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="e6e7e-149">Agregue y registre el elemento `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución hospedada tal y como se explica en la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="e6e7e-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="e6e7e-150">No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="e6e7e-151">En el archivo `Program.Main` de la aplicación independiente o la aplicación cliente de una solución hospedada, especifique la notificación denominada "`role`" como la notificación de rol:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="e6e7e-152">Los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="e6e7e-153">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="e6e7e-154">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="e6e7e-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="e6e7e-155">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="e6e7e-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="e6e7e-156">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="e6e7e-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="e6e7e-157">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="e6e7e-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="e6e7e-158">Identificadores de grupos de roles administrativos de AAD</span><span class="sxs-lookup"><span data-stu-id="e6e7e-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="e6e7e-159">Los identificadores de objeto reunidos en la siguiente tabla sirven para crear [directivas](xref:security/authorization/policies) para notificaciones `group`.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="e6e7e-160">Las directivas permiten a una aplicación autorizar a usuarios para que realicen diversas actividades en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e6e7e-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="e6e7e-161">Para más información, vea la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="e6e7e-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="e6e7e-162">Rol administrativo de AAD</span><span class="sxs-lookup"><span data-stu-id="e6e7e-162">AAD Administrative Role</span></span> | <span data-ttu-id="e6e7e-163">Id. de objeto</span><span class="sxs-lookup"><span data-stu-id="e6e7e-163">Object ID</span></span>
--- | ---
<span data-ttu-id="e6e7e-164">Administrador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="e6e7e-164">Application administrator</span></span> | <span data-ttu-id="e6e7e-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="e6e7e-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="e6e7e-166">Desarrollador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="e6e7e-166">Application developer</span></span> | <span data-ttu-id="e6e7e-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="e6e7e-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="e6e7e-168">Administrador de autenticación</span><span class="sxs-lookup"><span data-stu-id="e6e7e-168">Authentication administrator</span></span> | <span data-ttu-id="e6e7e-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="e6e7e-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="e6e7e-170">Administrador de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="e6e7e-170">Azure DevOps administrator</span></span> | <span data-ttu-id="e6e7e-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="e6e7e-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="e6e7e-172">Administrador de Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="e6e7e-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="e6e7e-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="e6e7e-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="e6e7e-174">Administrador de conjuntos de claves B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="e6e7e-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="e6e7e-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="e6e7e-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="e6e7e-176">Administrador de directivas B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="e6e7e-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="e6e7e-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="e6e7e-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="e6e7e-178">Administrador de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="e6e7e-178">B2C user flow administrator</span></span> | <span data-ttu-id="e6e7e-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="e6e7e-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="e6e7e-180">Administrador de atributos de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="e6e7e-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="e6e7e-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="e6e7e-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="e6e7e-182">Administrador de facturación</span><span class="sxs-lookup"><span data-stu-id="e6e7e-182">Billing administrator</span></span> | <span data-ttu-id="e6e7e-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="e6e7e-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="e6e7e-184">Administrador de aplicaciones en la nube</span><span class="sxs-lookup"><span data-stu-id="e6e7e-184">Cloud application administrator</span></span> | <span data-ttu-id="e6e7e-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="e6e7e-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="e6e7e-186">Administrador de dispositivos en la nube</span><span class="sxs-lookup"><span data-stu-id="e6e7e-186">Cloud device administrator</span></span> | <span data-ttu-id="e6e7e-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="e6e7e-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="e6e7e-188">Administrador de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="e6e7e-188">Compliance administrator</span></span> | <span data-ttu-id="e6e7e-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="e6e7e-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="e6e7e-190">Administrador de datos de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="e6e7e-190">Compliance data administrator</span></span> | <span data-ttu-id="e6e7e-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="e6e7e-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="e6e7e-192">Administrador de acceso condicional</span><span class="sxs-lookup"><span data-stu-id="e6e7e-192">Conditional Access administrator</span></span> | <span data-ttu-id="e6e7e-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="e6e7e-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="e6e7e-194">Aprobador del acceso a la Caja de seguridad del cliente</span><span class="sxs-lookup"><span data-stu-id="e6e7e-194">Customer LockBox access approver</span></span> | <span data-ttu-id="e6e7e-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="e6e7e-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="e6e7e-196">Administrador de Análisis de escritorio</span><span class="sxs-lookup"><span data-stu-id="e6e7e-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="e6e7e-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="e6e7e-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="e6e7e-198">Lectores de directorios</span><span class="sxs-lookup"><span data-stu-id="e6e7e-198">Directory readers</span></span> | <span data-ttu-id="e6e7e-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="e6e7e-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="e6e7e-200">Administrador de Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="e6e7e-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="e6e7e-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="e6e7e-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="e6e7e-202">Administrador de Exchange</span><span class="sxs-lookup"><span data-stu-id="e6e7e-202">Exchange administrator</span></span> | <span data-ttu-id="e6e7e-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="e6e7e-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="e6e7e-204">Administrador de proveedor de Identity externo</span><span class="sxs-lookup"><span data-stu-id="e6e7e-204">External Identity Provider administrator</span></span> | <span data-ttu-id="e6e7e-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="e6e7e-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="e6e7e-206">Administrador global</span><span class="sxs-lookup"><span data-stu-id="e6e7e-206">Global administrator</span></span> | <span data-ttu-id="e6e7e-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="e6e7e-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="e6e7e-208">Lector global</span><span class="sxs-lookup"><span data-stu-id="e6e7e-208">Global reader</span></span> | <span data-ttu-id="e6e7e-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="e6e7e-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="e6e7e-210">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="e6e7e-210">Groups administrator</span></span> | <span data-ttu-id="e6e7e-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="e6e7e-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="e6e7e-212">Invitador de usuarios</span><span class="sxs-lookup"><span data-stu-id="e6e7e-212">Guest inviter</span></span> | <span data-ttu-id="e6e7e-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="e6e7e-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="e6e7e-214">Administrador del departamento de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="e6e7e-214">Helpdesk administrator</span></span> | <span data-ttu-id="e6e7e-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="e6e7e-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="e6e7e-216">Administrador de Intune</span><span class="sxs-lookup"><span data-stu-id="e6e7e-216">Intune administrator</span></span> | <span data-ttu-id="e6e7e-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="e6e7e-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="e6e7e-218">Administrador de Kaizala</span><span class="sxs-lookup"><span data-stu-id="e6e7e-218">Kaizala administrator</span></span> | <span data-ttu-id="e6e7e-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="e6e7e-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="e6e7e-220">Administrador de licencias</span><span class="sxs-lookup"><span data-stu-id="e6e7e-220">License administrator</span></span> | <span data-ttu-id="e6e7e-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="e6e7e-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="e6e7e-222">Lector de privacidad del Centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="e6e7e-222">Message center privacy reader</span></span> | <span data-ttu-id="e6e7e-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="e6e7e-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="e6e7e-224">Lector del centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="e6e7e-224">Message center reader</span></span> | <span data-ttu-id="e6e7e-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="e6e7e-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="e6e7e-226">Administrador de aplicaciones de Office</span><span class="sxs-lookup"><span data-stu-id="e6e7e-226">Office apps administrator</span></span> | <span data-ttu-id="e6e7e-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="e6e7e-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="e6e7e-228">Administrador de contraseñas</span><span class="sxs-lookup"><span data-stu-id="e6e7e-228">Password administrator</span></span> | <span data-ttu-id="e6e7e-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="e6e7e-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="e6e7e-230">Administrador de Power BI</span><span class="sxs-lookup"><span data-stu-id="e6e7e-230">Power BI administrator</span></span> | <span data-ttu-id="e6e7e-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="e6e7e-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="e6e7e-232">Administrador de Power Platform</span><span class="sxs-lookup"><span data-stu-id="e6e7e-232">Power platform administrator</span></span> | <span data-ttu-id="e6e7e-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="e6e7e-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="e6e7e-234">Administrador de autenticación con privilegios</span><span class="sxs-lookup"><span data-stu-id="e6e7e-234">Privileged authentication administrator</span></span> | <span data-ttu-id="e6e7e-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="e6e7e-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="e6e7e-236">Administrador de roles con privilegios</span><span class="sxs-lookup"><span data-stu-id="e6e7e-236">Privileged role administrator</span></span> | <span data-ttu-id="e6e7e-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="e6e7e-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="e6e7e-238">Lector de informes</span><span class="sxs-lookup"><span data-stu-id="e6e7e-238">Reports reader</span></span> | <span data-ttu-id="e6e7e-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="e6e7e-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="e6e7e-240">Administrador de búsqueda</span><span class="sxs-lookup"><span data-stu-id="e6e7e-240">Search administrator</span></span> | <span data-ttu-id="e6e7e-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="e6e7e-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="e6e7e-242">Editor de búsqueda</span><span class="sxs-lookup"><span data-stu-id="e6e7e-242">Search editor</span></span> | <span data-ttu-id="e6e7e-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="e6e7e-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="e6e7e-244">Administrador de seguridad</span><span class="sxs-lookup"><span data-stu-id="e6e7e-244">Security administrator</span></span> | <span data-ttu-id="e6e7e-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="e6e7e-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="e6e7e-246">Operador de seguridad</span><span class="sxs-lookup"><span data-stu-id="e6e7e-246">Security operator</span></span> | <span data-ttu-id="e6e7e-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="e6e7e-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="e6e7e-248">Lector de seguridad</span><span class="sxs-lookup"><span data-stu-id="e6e7e-248">Security reader</span></span> | <span data-ttu-id="e6e7e-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="e6e7e-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="e6e7e-250">Administrador del soporte técnico del servicio</span><span class="sxs-lookup"><span data-stu-id="e6e7e-250">Service support administrator</span></span> | <span data-ttu-id="e6e7e-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="e6e7e-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="e6e7e-252">Administrador de SharePoint</span><span class="sxs-lookup"><span data-stu-id="e6e7e-252">SharePoint administrator</span></span> | <span data-ttu-id="e6e7e-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="e6e7e-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="e6e7e-254">Administrador de Skype Empresarial</span><span class="sxs-lookup"><span data-stu-id="e6e7e-254">Skype for Business administrator</span></span> | <span data-ttu-id="e6e7e-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="e6e7e-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="e6e7e-256">Administrador de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="e6e7e-256">Teams Communications Administrator</span></span> | <span data-ttu-id="e6e7e-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="e6e7e-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="e6e7e-258">Ingeniero de soporte técnico de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="e6e7e-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="e6e7e-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="e6e7e-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="e6e7e-260">Especialista de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="e6e7e-260">Teams Communications Specialist</span></span> | <span data-ttu-id="e6e7e-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="e6e7e-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="e6e7e-262">Administrador de servicios de Teams</span><span class="sxs-lookup"><span data-stu-id="e6e7e-262">Teams Service Administrator</span></span> | <span data-ttu-id="e6e7e-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="e6e7e-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="e6e7e-264">Administrador de usuarios</span><span class="sxs-lookup"><span data-stu-id="e6e7e-264">User administrator</span></span> | <span data-ttu-id="e6e7e-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="e6e7e-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6e7e-266">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e6e7e-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
