---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: b725a60a310be23f7ceb626d4c543d0df6fadf62
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394764"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="6dff0-103">Grupos, roles de administrador y roles de aplicación de Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="6dff0-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="6dff0-104">Azure Active Directory (AAD) proporciona diversos enfoques de autorización que se pueden combinar con ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="6dff0-104">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="6dff0-105">Grupos</span><span class="sxs-lookup"><span data-stu-id="6dff0-105">Groups</span></span>
  * <span data-ttu-id="6dff0-106">Seguridad</span><span class="sxs-lookup"><span data-stu-id="6dff0-106">Security</span></span>
  * <span data-ttu-id="6dff0-107">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="6dff0-107">Microsoft 365</span></span>
  * <span data-ttu-id="6dff0-108">Distribución</span><span class="sxs-lookup"><span data-stu-id="6dff0-108">Distribution</span></span>
* <span data-ttu-id="6dff0-109">Roles</span><span class="sxs-lookup"><span data-stu-id="6dff0-109">Roles</span></span>
  * <span data-ttu-id="6dff0-110">Roles de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="6dff0-110">AAD Administrator Roles</span></span>
  * <span data-ttu-id="6dff0-111">Roles de aplicación</span><span class="sxs-lookup"><span data-stu-id="6dff0-111">App Roles</span></span>

<span data-ttu-id="6dff0-112">Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:</span><span class="sxs-lookup"><span data-stu-id="6dff0-112">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="6dff0-113">Independiente con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="6dff0-113">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="6dff0-114">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="6dff0-114">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="6dff0-115">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="6dff0-115">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="6dff0-116">En la guía del artículo se proporcionan instrucciones para las aplicaciones de cliente y servidor:</span><span class="sxs-lookup"><span data-stu-id="6dff0-116">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="6dff0-117">**CLIENTE**: Aplicaciones Blazor WebAssembly independientes o la aplicación **`Client`** de una solución Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="6dff0-117">**CLIENT**: Standalone Blazor WebAssembly apps or the **`Client`** app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="6dff0-118">**SERVIDOR**: Aplicaciones de API web/API de servidor de ASP.NET Core independientes o la aplicación **`Server`** de una solución Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="6dff0-118">**SERVER**: Standalone ASP.NET Core server API/web API apps or the **`Server`** app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="6dff0-119">Ámbitos</span><span class="sxs-lookup"><span data-stu-id="6dff0-119">Scopes</span></span>

<span data-ttu-id="6dff0-120">Para permitir llamadas de [Microsoft Graph API](/graph/use-the-api) para los datos de perfil de usuario, asignación de roles y pertenencia a grupos, el **CLIENTE** se configura con (`https://graph.microsoft.com/User.Read`) [el permiso Graph API (ámbito)](/graph/permissions-reference) en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6dff0-120">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="6dff0-121">A una aplicación de **SERVIDOR** que llama a Graph API para obtener datos de pertenencia a roles y grupos se le proporciona `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [permiso de Graph API (ámbito)](/graph/permissions-reference) en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6dff0-121">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="6dff0-122">Estos ámbitos son necesarios además de los ámbitos requeridos en los escenarios de implementación de AAD descritos en los temas que se enumeran en la primera sección de este artículo.</span><span class="sxs-lookup"><span data-stu-id="6dff0-122">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="6dff0-123">Las palabras "permiso" y "ámbito" se usan indistintamente en el Azure Portal y en varios juegos de documentación tanto de Microsoft como externos.</span><span class="sxs-lookup"><span data-stu-id="6dff0-123">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="6dff0-124">En este artículo se utiliza la palabra "ámbito" para hacer referencia a los permisos asignados a una aplicación en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6dff0-124">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="6dff0-125">Atributo de notificaciones de pertenencia a grupos</span><span class="sxs-lookup"><span data-stu-id="6dff0-125">Group Membership Claims attribute</span></span>

<span data-ttu-id="6dff0-126">En el manifiesto de la aplicación de Azure Portal para las aplicaciones **CLIENTE** y **SERVIDOR**, establezca el [atributo `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) en `All`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-126">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="6dff0-127">Un valor de `All` se traduce en la obtención de todos los grupos de seguridad, grupos de distribución y roles de los que es miembro el usuario que inició sesión.</span><span class="sxs-lookup"><span data-stu-id="6dff0-127">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="6dff0-128">Abra el registro de Azure Portal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6dff0-128">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="6dff0-129">Seleccione **Administrar** > **Manifiesto** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="6dff0-129">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="6dff0-130">Busque el atributo `groupMembershipClaims`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-130">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="6dff0-131">Establezca el valor en `All`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-131">Set the value to `All`.</span></span>
1. <span data-ttu-id="6dff0-132">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="6dff0-132">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="6dff0-133">Cuenta de usuario personalizada</span><span class="sxs-lookup"><span data-stu-id="6dff0-133">Custom user account</span></span>

<span data-ttu-id="6dff0-134">Asigne usuarios a grupos de seguridad de AAD y roles de administrador de AAD en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6dff0-134">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="6dff0-135">En los ejemplos de este artículo:</span><span class="sxs-lookup"><span data-stu-id="6dff0-135">The examples in this article:</span></span>

* <span data-ttu-id="6dff0-136">Se supone que un usuario está asignado al rol de *administrador de facturación de AAD* en el inquilino de AAD de Azure Portal para que tenga autorización para acceder a los datos de la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="6dff0-136">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="6dff0-137">Se usan las [directivas de autorización](xref:security/authorization/policies) para controlar el acceso dentro de las aplicaciones **CLIENTE** y **SERVIDOR**.</span><span class="sxs-lookup"><span data-stu-id="6dff0-137">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="6dff0-138">En la aplicación **CLIENTE**, extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de:</span><span class="sxs-lookup"><span data-stu-id="6dff0-138">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="6dff0-139">`Roles`; Matriz de roles de aplicación de AAD (se trata en la sección [Roles de aplicación](#app-roles))</span><span class="sxs-lookup"><span data-stu-id="6dff0-139">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="6dff0-140">`Wids`; Roles de administrador de AAD en [notificaciones de identificadores conocidos (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="6dff0-140">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="6dff0-141">`Oid`; [Notificación de identificador de objeto (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) inmutable (identifica de forma única a un usuario dentro de los inquilinos y entre ellos)</span><span class="sxs-lookup"><span data-stu-id="6dff0-141">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="6dff0-142">Asigne una matriz vacía a cada propiedad de matriz para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-142">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="6dff0-143">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-143">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="6dff0-144">Agregue una referencia de paquete al archivo de proyecto de la aplicación **CLIENTE** para [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span><span class="sxs-lookup"><span data-stu-id="6dff0-144">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="6dff0-145">Agregue la configuración y las clases de utilidad del SDK de Graph de la sección *SDK de Graph* del artículo <xref:blazor/security/webassembly/graph-api#graph-sdk>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-145">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="6dff0-146">En la clase `GraphClientExtensions`, especifique el ámbito de `User.Read` para el token de acceso en el método `AuthenticateRequestAsync`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-146">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="6dff0-147">Agregue la siguiente fábrica de cuentas de usuario personalizada a la aplicación **CLIENTE**.</span><span class="sxs-lookup"><span data-stu-id="6dff0-147">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="6dff0-148">La fábrica de usuarios personalizada se usa para establecer:</span><span class="sxs-lookup"><span data-stu-id="6dff0-148">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="6dff0-149">Notificaciones de rol de aplicación (`appRole`) (se trata en la sección [Roles de aplicación](#app-roles)).</span><span class="sxs-lookup"><span data-stu-id="6dff0-149">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="6dff0-150">Notificaciones de rol de administrador de AAD (`directoryRole`).</span><span class="sxs-lookup"><span data-stu-id="6dff0-150">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="6dff0-151">Un ejemplo de notificación de datos de perfil de usuario para el número de teléfono móvil del usuario (`mobilePhone`).</span><span class="sxs-lookup"><span data-stu-id="6dff0-151">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="6dff0-152">Notificaciones de grupo de AAD (`directoryGroup`).</span><span class="sxs-lookup"><span data-stu-id="6dff0-152">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="6dff0-153">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-153">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
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
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="6dff0-154">El código anterior no incluye pertenencias transitivas.</span><span class="sxs-lookup"><span data-stu-id="6dff0-154">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="6dff0-155">Si la aplicación requiere notificaciones directas y transitivas de pertenencia a grupos, reemplace la propiedad `MemberOf` (`IUserMemberOfCollectionWithReferencesRequestBuilder`) por `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span><span class="sxs-lookup"><span data-stu-id="6dff0-155">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="6dff0-156">En el código anterior se omiten las notificaciones de pertenencia a grupos (`groups`) que son roles de administrador de AAD (tipo `#microsoft.graph.directoryRole`) porque los valores de GUID devueltos por Microsoft Identity Platform 2.0 son **identificadores de entidad** del rol de administrador de AAD y no [**identificadores de plantilla de rol**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="6dff0-156">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="6dff0-157">Los identificadores de entidad no son estables en los inquilinos de Microsoft Identity Platform 2.0 y no deben usarse para crear directivas de autorización para los usuarios de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6dff0-157">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="6dff0-158">Use siempre **identificadores de plantilla de rol** para roles de administrador de AAD **proporcionados por notificaciones `wids`** .</span><span class="sxs-lookup"><span data-stu-id="6dff0-158">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="6dff0-159">En `Program.Main` de la aplicación **CLIENTE**, configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada.</span><span class="sxs-lookup"><span data-stu-id="6dff0-159">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="6dff0-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-160">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="6dff0-161">Configuración de autorización</span><span class="sxs-lookup"><span data-stu-id="6dff0-161">Authorization configuration</span></span>

<span data-ttu-id="6dff0-162">En la aplicación **CLIENTE**, cree una [directiva](xref:security/authorization/policies) para cada [rol de aplicación](#app-roles), rol de administrador de ADD o grupo de seguridad en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-162">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="6dff0-163">En el siguiente ejemplo se crea una directiva para el rol *Administrador de facturación* de AAD:</span><span class="sxs-lookup"><span data-stu-id="6dff0-163">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="6dff0-164">Para obtener una lista completa de los identificadores de los roles de administrador de AAD, consulte [Identificadores de plantilla de rol](/azure/active-directory/roles/permissions-reference#role-template-ids) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="6dff0-164">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="6dff0-165">Para obtener más información sobre las directivas de autorización, vea <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-165">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6dff0-166">En los siguientes ejemplos, la aplicación **CLIENTE** usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="6dff0-166">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="6dff0-167">El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:</span><span class="sxs-lookup"><span data-stu-id="6dff0-167">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
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

<span data-ttu-id="6dff0-168">El acceso a un componente completo se puede basar en la directiva usando una [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="6dff0-168">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="6dff0-169">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="6dff0-169">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="6dff0-170">También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="6dff0-170">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="6dff0-171">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-171">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="6dff0-172">Autorización del acceso a la API web/API de servidor</span><span class="sxs-lookup"><span data-stu-id="6dff0-172">Authorize server API/web API access</span></span>

<span data-ttu-id="6dff0-173">Una aplicación de API de **SERVIDOR** puede autorizar a los usuarios para que accedan a puntos de conexión de API seguros con [directivas de autorización](xref:security/authorization/policies) para grupos de seguridad, roles de administrador de AAD y roles de aplicación cuando un token de acceso contiene notificaciones `groups`, `wids`y `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.</span><span class="sxs-lookup"><span data-stu-id="6dff0-173">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="6dff0-174">En el ejemplo siguiente se crea una directiva para el rol de *administrador de facturación* de AAD en `Startup.ConfigureServices` con las notificaciones de `wids` (identificadores conocidos y de plantilla de rol):</span><span class="sxs-lookup"><span data-stu-id="6dff0-174">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="6dff0-175">Para obtener una lista completa de los identificadores de los roles de administrador de AAD, consulte [Identificadores de plantilla de rol](/azure/active-directory/roles/permissions-reference#role-template-ids) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="6dff0-175">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="6dff0-176">Para obtener más información sobre las directivas de autorización, vea <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-176">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6dff0-177">El acceso a un controlador de la aplicación  **SERVIDOR** puede basarse en el uso de un [atributo `[Authorize]`](xref:security/authorization/simple) con el nombre de la directiva (documentación de la API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span><span class="sxs-lookup"><span data-stu-id="6dff0-177">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="6dff0-178">En el siguiente ejemplo se limita el acceso a los datos de facturación del elemento `BillingDataController` a los administradores de facturación de Azure con un nombre de directiva de `BillingAdministrator`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-178">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="6dff0-179">Para obtener más información, vea <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-179">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="6dff0-180">Roles de aplicación</span><span class="sxs-lookup"><span data-stu-id="6dff0-180">App Roles</span></span>

<span data-ttu-id="6dff0-181">Para configurar la aplicación en Azure Portal para proporcionar notificaciones de pertenencia de roles de aplicación, consulte [Procedimiento: agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="6dff0-181">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="6dff0-182">En el ejemplo siguiente se da por supuesto que las aplicaciones **CLIENTE** y **SERVIDOR** están configuradas con dos roles y los roles están asignados a un usuario de prueba:</span><span class="sxs-lookup"><span data-stu-id="6dff0-182">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="6dff0-183">Al desarrollar una aplicación Blazor WebAssembly hospedada o un par cliente-servidor de aplicaciones independientes (una aplicación Blazor WebAssembly independiente y una aplicación de API web/API de servidor de ASP.NET Core independiente), la propiedad del manifiesto `appRoles` de los registros de aplicaciones de Azure Portal de cliente y servidor deben incluir los mismos roles configurados.</span><span class="sxs-lookup"><span data-stu-id="6dff0-183">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="6dff0-184">Después de establecer los roles en el manifiesto de la aplicación del cliente, cópielos en su totalidad en el manifiesto de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="6dff0-184">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="6dff0-185">Si no refleja el manifiesto `appRoles` entre los registros de aplicaciones de cliente y de servidor, las notificaciones de rol no se establecen para los usuarios autenticados de la API web o la API de servidor, aunque su token de acceso tenga las notificaciones de roles correctas.</span><span class="sxs-lookup"><span data-stu-id="6dff0-185">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="6dff0-186">Aunque no se pueden asignar roles a grupos sin una cuenta de Azure AD Premium, sí se pueden asignar roles a usuarios y recibir una notificación de roles relativa a los usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="6dff0-186">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="6dff0-187">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="6dff0-187">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="6dff0-188">Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.</span><span class="sxs-lookup"><span data-stu-id="6dff0-188">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="6dff0-189">El valor `CustomAccountFactory` que se muestra en la sección [Cuenta de usuario personalizada](#custom-user-account) se configura para que actúe en una notificación de `roles` con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="6dff0-189">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="6dff0-190">Agregue y registre `CustomAccountFactory` en la aplicación **CLIENTE** como se muestra en la sección [Cuenta de usuario personalizada](#custom-user-account).</span><span class="sxs-lookup"><span data-stu-id="6dff0-190">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="6dff0-191">No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="6dff0-191">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="6dff0-192">En `Program.Main` de una aplicación **CLIENTE**, especifique la notificación denominada "`appRole`" como la notificación de rol para las comprobaciones <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="6dff0-192">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="6dff0-193">Si prefiere usar la notificación `directoryRoles` (roles de administrador de ADD), asigne "`directoryRoles`" a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-193">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="6dff0-194">En `Startup.ConfigureServices` de una aplicación **SERVIDOR**, especifique la notificación denominada "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" como la notificación de rol para las comprobaciones <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="6dff0-194">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="6dff0-195">Si prefiere usar la notificación `wids` (roles de administrador de ADD), asigne "`wids`" a <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6dff0-195">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="6dff0-196">Los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="6dff0-196">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="6dff0-197">Cualquiera de los mecanismos de autorización de los componentes de la aplicación **CLIENTE** puede usar el rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="6dff0-197">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="6dff0-198">Componente `AuthorizeView`</span><span class="sxs-lookup"><span data-stu-id="6dff0-198">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="6dff0-199">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="6dff0-199">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="6dff0-200">Lógica de procedimientos</span><span class="sxs-lookup"><span data-stu-id="6dff0-200">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="6dff0-201">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="6dff0-201">Multiple role tests are supported:</span></span>

* <span data-ttu-id="6dff0-202">Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el componente `AuthorizeView`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-202">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="6dff0-203">Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el componente `AuthorizeView`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-203">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="6dff0-204">Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-204">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="6dff0-205">Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-205">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="6dff0-206">Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el código de procedimientos:</span><span class="sxs-lookup"><span data-stu-id="6dff0-206">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="6dff0-207">Se requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el código de procedimientos cambiando el [OR condicional (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) a un [AND condicional (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="6dff0-207">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="6dff0-208">Cualquiera de los mecanismos de autorización de los controladores de la aplicación **SERVIDOR** puede usar el rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="6dff0-208">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="6dff0-209">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="6dff0-209">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="6dff0-210">Lógica de procedimientos</span><span class="sxs-lookup"><span data-stu-id="6dff0-210">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="6dff0-211">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="6dff0-211">Multiple role tests are supported:</span></span>

* <span data-ttu-id="6dff0-212">Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-212">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="6dff0-213">Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="6dff0-213">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="6dff0-214">Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el código de procedimientos:</span><span class="sxs-lookup"><span data-stu-id="6dff0-214">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="6dff0-215">Se requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el código de procedimientos cambiando el [OR condicional (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) a un [AND condicional (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="6dff0-215">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="6dff0-216">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6dff0-216">Additional resources</span></span>

* [<span data-ttu-id="6dff0-217">Identificadores de plantilla de rol (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="6dff0-217">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="6dff0-218">Atributo `groupMembershipClaims` (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="6dff0-218">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="6dff0-219">Cómo: Incorporación de roles de aplicación a una aplicación y su recepción en el token (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="6dff0-219">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="6dff0-220">Roles de aplicación (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="6dff0-220">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
