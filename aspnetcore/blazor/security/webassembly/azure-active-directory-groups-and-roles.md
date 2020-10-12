---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636782"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="50dbc-103">Grupos, roles de administrador y roles definidos por el usuario de Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="50dbc-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="50dbc-104">Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="50dbc-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="50dbc-105">Azure Active Directory (AAD) proporciona diversos enfoques de autorización que se pueden combinar con ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="50dbc-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="50dbc-106">Grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="50dbc-106">User-defined groups</span></span>
  * <span data-ttu-id="50dbc-107">Seguridad</span><span class="sxs-lookup"><span data-stu-id="50dbc-107">Security</span></span>
  * <span data-ttu-id="50dbc-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="50dbc-108">Microsoft 365</span></span>
  * <span data-ttu-id="50dbc-109">Distribución</span><span class="sxs-lookup"><span data-stu-id="50dbc-109">Distribution</span></span>
* <span data-ttu-id="50dbc-110">Roles</span><span class="sxs-lookup"><span data-stu-id="50dbc-110">Roles</span></span>
  * <span data-ttu-id="50dbc-111">Roles de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="50dbc-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="50dbc-112">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="50dbc-112">User-defined roles</span></span>

<span data-ttu-id="50dbc-113">Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:</span><span class="sxs-lookup"><span data-stu-id="50dbc-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="50dbc-114">Independiente con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="50dbc-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="50dbc-115">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="50dbc-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="50dbc-116">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="50dbc-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="50dbc-117">Permisos para Microsoft Graph API</span><span class="sxs-lookup"><span data-stu-id="50dbc-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="50dbc-118">Para los usuarios de aplicaciones con más de cinco pertenencias a grupos de seguridad y roles de administrador de AAD, se requiere una llamada [Microsoft Graph API](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="50dbc-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="50dbc-119">Para permitir llamadas a Graph API, proporcione a la aplicación cliente o independiente de una solución de Blazor hospedada cualquiera de los siguientes [permisos de Graph API](/graph/permissions-reference) en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="50dbc-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="50dbc-120">`Directory.Read.All` es el permiso con privilegios mínimos y es el que se usa para el ejemplo que se describe en este artículo.</span><span class="sxs-lookup"><span data-stu-id="50dbc-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="50dbc-121">Roles de administrador y grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="50dbc-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="50dbc-122">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia de tipo `groups`, vea los siguientes artículos sobre Azure.</span><span class="sxs-lookup"><span data-stu-id="50dbc-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="50dbc-123">Asigne usuarios a grupos definidos por el usuario y roles de administrador de AAD.</span><span class="sxs-lookup"><span data-stu-id="50dbc-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="50dbc-124">Roles que usan grupos de seguridad de Azure AD</span><span class="sxs-lookup"><span data-stu-id="50dbc-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="50dbc-125">Atributo `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="50dbc-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="50dbc-126">En los siguientes ejemplos se da por hecho que a un usuario se le asigna el rol *Administrador de facturación*.</span><span class="sxs-lookup"><span data-stu-id="50dbc-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="50dbc-127">La notificación `groups` única enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="50dbc-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="50dbc-128">La aplicación debe convertir esta matriz JSON de grupos y roles en notificaciones `group` individuales, a partir de las cuales la aplicación podrá crear [directivas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="50dbc-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="50dbc-129">Cuando el número de roles de administrador y grupos definidos por el usuario de AAD asignados es superior a cinco, AAD envía una notificación `hasgroups` con un valor `true` en lugar de enviar una notificación `groups`.</span><span class="sxs-lookup"><span data-stu-id="50dbc-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="50dbc-130">Cualquier aplicación que pueda tener más de cinco roles y grupos asignados a sus usuarios debe realizar una llamada a Graph API independiente para obtener los roles y grupos de un usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="50dbc-131">En la implementación de ejemplo que se proporciona en este artículo se aborda este escenario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="50dbc-132">Para más información, vea la información sobre las notificaciones `groups` y `hasgroups` en el artículo [Tokens de acceso de la Plataforma de identidad de Microsoft: Notificaciones de carga](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="50dbc-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="50dbc-133">Extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz de grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="50dbc-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="50dbc-134">Asigne una matriz vacía a cada propiedad para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach` más adelante.</span><span class="sxs-lookup"><span data-stu-id="50dbc-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="50dbc-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="50dbc-136">En la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, cree una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada.</span><span class="sxs-lookup"><span data-stu-id="50dbc-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="50dbc-137">Utilice el ámbito correcto (permiso) para las llamadas a Graph API que obtienen información de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="50dbc-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="50dbc-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="50dbc-139">En `Program.Main` (`Program.cs`), agregue el servicio de implementación <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> y agregue un elemento con nombre <xref:System.Net.Http.HttpClient> para realizar solicitudes a Graph API.</span><span class="sxs-lookup"><span data-stu-id="50dbc-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="50dbc-140">En el siguiente ejemplo se nombra el cliente `GraphAPI`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="50dbc-141">Cree clases de objetos de directorio de AAD para recibir los roles y grupos de Open Data Protocol (OData) desde una llamada a Graph API.</span><span class="sxs-lookup"><span data-stu-id="50dbc-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="50dbc-142">OData se presenta en formato JSON, y una llamada a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> rellena una instancia de la clase `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="50dbc-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="50dbc-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="50dbc-144">Cree una fábrica de usuario personalizada para procesar notificaciones de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="50dbc-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="50dbc-145">En la implementación de ejemplo siguiente también se controla la matriz de notificaciones `roles`, que se describe en la sección [Roles definidos por el usuario](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="50dbc-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="50dbc-146">Si la notificación `hasgroups` está presente, el elemento <xref:System.Net.Http.HttpClient> con nombre se usa para hacer una solicitud autorizada a Graph API para obtener los roles y grupos del usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="50dbc-147">Esta implementación usa el punto de conexión de la plataforma de Identity de Microsoft v1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentación de API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="50dbc-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="50dbc-148">Las instrucciones de este tema se actualizarán para Identity v2.0 cuando se actualicen los paquetes MSAL para v2.0.</span><span class="sxs-lookup"><span data-stu-id="50dbc-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="50dbc-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="50dbc-150">No es necesario incluir código para quitar la notificación `groups` original, si está presente, ya que el marco de trabajo la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="50dbc-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="50dbc-151">El enfoque en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="50dbc-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="50dbc-152">Agrega una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada para adjuntar tokens de acceso a solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="50dbc-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="50dbc-153">Agrega un elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes de API web a un punto de conexión de API web externo seguro.</span><span class="sxs-lookup"><span data-stu-id="50dbc-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="50dbc-154">Utiliza el elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes autorizadas.</span><span class="sxs-lookup"><span data-stu-id="50dbc-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="50dbc-155">La cobertura general de este enfoque se encuentra en el artículo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="50dbc-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="50dbc-156">Registre la fábrica en el archivo `Program.Main` (`Program.cs`) de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="50dbc-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="50dbc-157">Dé el consentimiento en el ámbito de permiso de `Directory.Read.All` como un ámbito adicional para la aplicación:</span><span class="sxs-lookup"><span data-stu-id="50dbc-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="50dbc-158">Cree una [directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="50dbc-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="50dbc-159">En el siguiente ejemplo se crea una directiva para el rol *Administrador de facturación* de AAD:</span><span class="sxs-lookup"><span data-stu-id="50dbc-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="50dbc-160">Para ver una lista completa de identificadores de objeto de rol de AAD, consulte la sección [Identificadores de objeto de rol de administrador de AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="50dbc-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="50dbc-161">En los siguientes ejemplos, la aplicación usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="50dbc-162">El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:</span><span class="sxs-lookup"><span data-stu-id="50dbc-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="50dbc-163">El acceso a un componente completo se puede basar en la directiva usando la [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="50dbc-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="50dbc-164">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="50dbc-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="50dbc-165">También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="50dbc-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="50dbc-166">Autorización de acceso de la API de servidor para roles de administrador y grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="50dbc-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="50dbc-167">Además de autorizar a los usuarios de la aplicación WebAssembly del lado cliente el acceso a páginas y recursos, la API de servidor puede autorizar a los usuarios el acceso a puntos de conexión de API seguros.</span><span class="sxs-lookup"><span data-stu-id="50dbc-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="50dbc-168">Una vez que la aplicación *Server* valida el token de acceso del usuario:</span><span class="sxs-lookup"><span data-stu-id="50dbc-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="50dbc-169">La aplicación usa la [notificación de identificador de objeto inmutable (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) de JWT (`id_token`) para obtener un token de acceso para Graph API.</span><span class="sxs-lookup"><span data-stu-id="50dbc-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="50dbc-170">Con una llamada Graph API se obtiene el grupo de seguridad definido por el usuario y las pertenencias a roles de administrador de Azure.</span><span class="sxs-lookup"><span data-stu-id="50dbc-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="50dbc-171">Las pertenencias se usan para establecer notificaciones `group` .</span><span class="sxs-lookup"><span data-stu-id="50dbc-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="50dbc-172">Para limitar el acceso de los usuarios a los puntos de conexión de API de servidor, se pueden usar [directivas de autorización](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="50dbc-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="50dbc-173">Esta guía no incluye actualmente la autorización de usuarios en función de sus [roles definidos por el usuario de AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="50dbc-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="50dbc-174">Paquetes</span><span class="sxs-lookup"><span data-stu-id="50dbc-174">Packages</span></span>

<span data-ttu-id="50dbc-175">Agregue referencias de paquete a la aplicación *Server* para los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="50dbc-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="50dbc-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="50dbc-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="50dbc-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="50dbc-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="50dbc-178">Configuración de Azure</span><span class="sxs-lookup"><span data-stu-id="50dbc-178">Azure configuration</span></span>

* <span data-ttu-id="50dbc-179">Confirme que al registro de la aplicación *Server* se le proporciona acceso de API al permiso de Graph API para `Directory.Read.All`, que es el nivel de acceso con privilegios mínimos de los grupos de seguridad.</span><span class="sxs-lookup"><span data-stu-id="50dbc-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="50dbc-180">Confirme que el consentimiento del administrador se aplica al permiso después de realizar la asignación de permisos.</span><span class="sxs-lookup"><span data-stu-id="50dbc-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="50dbc-181">Asigne un nuevo secreto de cliente a la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="50dbc-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="50dbc-182">Fíjese en el secreto de la configuración de la aplicación de la sección [Configuración de la aplicación](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="50dbc-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="50dbc-183">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="50dbc-183">App settings</span></span>

<span data-ttu-id="50dbc-184">En el archivo de configuración de la aplicación (`appsettings.json` o `appsettings.Production.json`), cree una entrada `ClientSecret` con el secreto de cliente de la aplicación *Server* desde Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="50dbc-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="50dbc-185">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="50dbc-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="50dbc-186">Directivas de autorización</span><span class="sxs-lookup"><span data-stu-id="50dbc-186">Authorization policies</span></span>

<span data-ttu-id="50dbc-187">Cree [directivas de autorización](xref:security/authorization/policies) para los grupos de seguridad y los roles de administrador de AAD en el elemento `Startup.ConfigureServices` de la aplicación *Server*(`Startup.cs`) en función de los identificadores de objeto de grupo y de los [identificadores de objeto de rol de administrador de AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="50dbc-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="50dbc-188">Por ejemplo, una directiva de rol de administrador de facturación de Azure tiene la siguiente configuración:</span><span class="sxs-lookup"><span data-stu-id="50dbc-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="50dbc-189">Para más información, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="50dbc-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="50dbc-190">Acceso del controlador</span><span class="sxs-lookup"><span data-stu-id="50dbc-190">Controller access</span></span>

<span data-ttu-id="50dbc-191">Exija directivas en los controladores de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="50dbc-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="50dbc-192">En el siguiente ejemplo se limita el acceso a los datos de facturación del elemento `BillingDataController` a los administradores de facturación de Azure con un nombre de directiva de `BillingAdmin`, según se ha configurado en la sección [Directivas de autorización](#authorization-policies):</span><span class="sxs-lookup"><span data-stu-id="50dbc-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="50dbc-193">Configuración del servicio</span><span class="sxs-lookup"><span data-stu-id="50dbc-193">Service configuration</span></span>

<span data-ttu-id="50dbc-194">En el método `Startup.ConfigureServices` de la aplicación *Server*, agregue lógica para realizar la llamada Graph API y establezca notificaciones `group` de usuario para los roles y grupos de seguridad del usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="50dbc-195">En el código de ejemplo de esta sección se usa el Biblioteca de autenticación de Active Directory (ADAL), que se basa en Microsoft Identity Platform v1.0.</span><span class="sxs-lookup"><span data-stu-id="50dbc-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="50dbc-196">Este tema se actualizará para Identity v2.0 para .NET 5.</span><span class="sxs-lookup"><span data-stu-id="50dbc-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="50dbc-197">Siga el progreso de este trabajo mediante la supervisión de [[RC1] Microsoft Identity Platform 2.0 para Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="50dbc-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="50dbc-198">Se requieren espacios de nombres adicionales para el código de la clase `Startup` de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="50dbc-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="50dbc-199">El siguiente conjunto de instrucciones `using` incluye los espacios de nombres necesarios para el código que se muestra a continuación en esta sección:</span><span class="sxs-lookup"><span data-stu-id="50dbc-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="50dbc-200">Al configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="50dbc-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="50dbc-201">Tiene la opción de incluir el procesamiento de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="50dbc-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="50dbc-202">Por ejemplo, la aplicación puede registrar un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="50dbc-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="50dbc-203">En <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, realice una llamada Graph API para obtener los grupos y los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="50dbc-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> proporciona información de identificación personal (PII) en los mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="50dbc-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="50dbc-205">Active PII únicamente para la depuración con cuentas de usuario de prueba.</span><span class="sxs-lookup"><span data-stu-id="50dbc-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="50dbc-206">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50dbc-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="50dbc-207">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="50dbc-207">In the preceding example:</span></span>

* <span data-ttu-id="50dbc-208">Primero se intenta la adquisición silenciosa del token (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) porque el token de acceso puede haberse almacenado ya en la caché de tokens de ADAL.</span><span class="sxs-lookup"><span data-stu-id="50dbc-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="50dbc-209">Es más rápido obtener el token de la caché que solicitar uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="50dbc-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="50dbc-210">Si el token de acceso no se adquiere de la caché (se produce <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>), se realiza una aserción de usuario (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) con la credencial de cliente (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) para obtener el token en nombre del usuario (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="50dbc-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="50dbc-211">Luego, `Microsoft.Graph.GraphServiceClient` puede proceder a usar el token para realizar la llamada Graph API.</span><span class="sxs-lookup"><span data-stu-id="50dbc-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="50dbc-212">El token se coloca en la caché de tokens de ADAL.</span><span class="sxs-lookup"><span data-stu-id="50dbc-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="50dbc-213">En las llamadas Graph API futuras correspondientes al mismo usuario, el token se adquiere de la caché de forma silenciosa con <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="50dbc-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="50dbc-214">El código de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> no obtiene pertenencias transitivas.</span><span class="sxs-lookup"><span data-stu-id="50dbc-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="50dbc-215">Para cambiar el código a fin de obtener pertenencias directas y transitivas:</span><span class="sxs-lookup"><span data-stu-id="50dbc-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="50dbc-216">En la línea de código:</span><span class="sxs-lookup"><span data-stu-id="50dbc-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="50dbc-217">Reemplace la línea anterior por:</span><span class="sxs-lookup"><span data-stu-id="50dbc-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="50dbc-218">En la línea de código:</span><span class="sxs-lookup"><span data-stu-id="50dbc-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="50dbc-219">Reemplace la línea anterior por:</span><span class="sxs-lookup"><span data-stu-id="50dbc-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="50dbc-220">Al crear notificaciones, el código de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> no distingue entre grupos de seguridad y roles de administrador de AAD.</span><span class="sxs-lookup"><span data-stu-id="50dbc-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="50dbc-221">Para que lo haga, compruebe el valor de `entry.ODataType` al recorrer en iteración los grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="50dbc-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="50dbc-222">Para crear notificaciones de rol y de grupo de seguridad por separado, use código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="50dbc-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="50dbc-223">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="50dbc-223">User-defined roles</span></span>

<span data-ttu-id="50dbc-224">Una aplicación registrada en AAD también se puede configurar para usar roles definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="50dbc-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="50dbc-225">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia `roles`, vea [Procedimiento para agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="50dbc-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="50dbc-226">En el siguiente ejemplo se da por hecho que una aplicación está configurada con dos roles:</span><span class="sxs-lookup"><span data-stu-id="50dbc-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="50dbc-227">Aunque no se pueden asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, sí se pueden asignar usuarios a roles y recibir una notificación `roles` relativa a los usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="50dbc-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="50dbc-228">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="50dbc-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="50dbc-229">Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.</span><span class="sxs-lookup"><span data-stu-id="50dbc-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="50dbc-230">La notificación `roles` única enviada por AAD presenta los roles definidos por el usuario como valores `value` de `appRoles` en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="50dbc-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="50dbc-231">La aplicación debe convertir esta matriz JSON de roles en notificaciones `role` individuales.</span><span class="sxs-lookup"><span data-stu-id="50dbc-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="50dbc-232">El elemento `CustomUserFactory` que se muestra en la sección [Grupos definidos por el usuario y roles de administrador de ADD](#user-defined-groups-and-administrator-roles) está configurado para actuar a partir de una notificación `roles` con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="50dbc-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="50dbc-233">Agregue y registre el elemento `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución Blazor hospedada, como se muestra en la sección [Grupos definidos por el usuario y roles de administrador de AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="50dbc-233">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="50dbc-234">No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="50dbc-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="50dbc-235">En el archivo `Program.Main` de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, especifique la notificación denominada "`role`" como la notificación de rol:</span><span class="sxs-lookup"><span data-stu-id="50dbc-235">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="50dbc-236">Los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="50dbc-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="50dbc-237">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="50dbc-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="50dbc-238">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="50dbc-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="50dbc-239">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="50dbc-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="50dbc-240">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="50dbc-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="50dbc-241">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="50dbc-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="50dbc-242">Identificadores de objeto de rol de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="50dbc-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="50dbc-243">Los identificadores de objeto reunidos en la siguiente tabla sirven para crear [directivas](xref:security/authorization/policies) para notificaciones `group`.</span><span class="sxs-lookup"><span data-stu-id="50dbc-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="50dbc-244">Las directivas permiten a una aplicación autorizar a usuarios para que realicen diversas actividades en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="50dbc-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="50dbc-245">Para más información, consulte la sección [Grupos definidos por el usuario y roles de administrador de AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="50dbc-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="50dbc-246">Rol de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="50dbc-246">AAD Administrator Role</span></span> | <span data-ttu-id="50dbc-247">Id. de objeto</span><span class="sxs-lookup"><span data-stu-id="50dbc-247">Object ID</span></span>
--- | ---
<span data-ttu-id="50dbc-248">Administrador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="50dbc-248">Application administrator</span></span> | <span data-ttu-id="50dbc-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="50dbc-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="50dbc-250">Desarrollador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="50dbc-250">Application developer</span></span> | <span data-ttu-id="50dbc-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="50dbc-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="50dbc-252">Administrador de autenticación</span><span class="sxs-lookup"><span data-stu-id="50dbc-252">Authentication administrator</span></span> | <span data-ttu-id="50dbc-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="50dbc-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="50dbc-254">Administrador de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="50dbc-254">Azure DevOps administrator</span></span> | <span data-ttu-id="50dbc-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="50dbc-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="50dbc-256">Administrador de Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="50dbc-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="50dbc-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="50dbc-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="50dbc-258">Administrador de conjuntos de claves B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="50dbc-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="50dbc-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="50dbc-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="50dbc-260">Administrador de directivas B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="50dbc-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="50dbc-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="50dbc-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="50dbc-262">Administrador de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="50dbc-262">B2C user flow administrator</span></span> | <span data-ttu-id="50dbc-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="50dbc-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="50dbc-264">Administrador de atributos de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="50dbc-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="50dbc-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="50dbc-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="50dbc-266">Administrador de facturación</span><span class="sxs-lookup"><span data-stu-id="50dbc-266">Billing administrator</span></span> | <span data-ttu-id="50dbc-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="50dbc-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="50dbc-268">Administrador de aplicaciones en la nube</span><span class="sxs-lookup"><span data-stu-id="50dbc-268">Cloud application administrator</span></span> | <span data-ttu-id="50dbc-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="50dbc-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="50dbc-270">Administrador de dispositivos en la nube</span><span class="sxs-lookup"><span data-stu-id="50dbc-270">Cloud device administrator</span></span> | <span data-ttu-id="50dbc-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="50dbc-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="50dbc-272">Administrador de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="50dbc-272">Compliance administrator</span></span> | <span data-ttu-id="50dbc-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="50dbc-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="50dbc-274">Administrador de datos de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="50dbc-274">Compliance data administrator</span></span> | <span data-ttu-id="50dbc-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="50dbc-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="50dbc-276">Administrador de acceso condicional</span><span class="sxs-lookup"><span data-stu-id="50dbc-276">Conditional Access administrator</span></span> | <span data-ttu-id="50dbc-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="50dbc-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="50dbc-278">Aprobador del acceso a la Caja de seguridad del cliente</span><span class="sxs-lookup"><span data-stu-id="50dbc-278">Customer LockBox access approver</span></span> | <span data-ttu-id="50dbc-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="50dbc-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="50dbc-280">Administrador de Análisis de escritorio</span><span class="sxs-lookup"><span data-stu-id="50dbc-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="50dbc-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="50dbc-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="50dbc-282">Lectores de directorios</span><span class="sxs-lookup"><span data-stu-id="50dbc-282">Directory readers</span></span> | <span data-ttu-id="50dbc-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="50dbc-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="50dbc-284">Administrador de Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="50dbc-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="50dbc-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="50dbc-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="50dbc-286">Administrador de Exchange</span><span class="sxs-lookup"><span data-stu-id="50dbc-286">Exchange administrator</span></span> | <span data-ttu-id="50dbc-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="50dbc-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="50dbc-288">Administrador de proveedor de Identity externo</span><span class="sxs-lookup"><span data-stu-id="50dbc-288">External Identity Provider administrator</span></span> | <span data-ttu-id="50dbc-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="50dbc-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="50dbc-290">Administrador global</span><span class="sxs-lookup"><span data-stu-id="50dbc-290">Global administrator</span></span> | <span data-ttu-id="50dbc-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="50dbc-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="50dbc-292">Lector global</span><span class="sxs-lookup"><span data-stu-id="50dbc-292">Global reader</span></span> | <span data-ttu-id="50dbc-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="50dbc-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="50dbc-294">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="50dbc-294">Groups administrator</span></span> | <span data-ttu-id="50dbc-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="50dbc-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="50dbc-296">Invitador de usuarios</span><span class="sxs-lookup"><span data-stu-id="50dbc-296">Guest inviter</span></span> | <span data-ttu-id="50dbc-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="50dbc-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="50dbc-298">Administrador del departamento de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="50dbc-298">Helpdesk administrator</span></span> | <span data-ttu-id="50dbc-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="50dbc-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="50dbc-300">Administrador de Intune</span><span class="sxs-lookup"><span data-stu-id="50dbc-300">Intune administrator</span></span> | <span data-ttu-id="50dbc-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="50dbc-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="50dbc-302">Administrador de Kaizala</span><span class="sxs-lookup"><span data-stu-id="50dbc-302">Kaizala administrator</span></span> | <span data-ttu-id="50dbc-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="50dbc-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="50dbc-304">Administrador de licencias</span><span class="sxs-lookup"><span data-stu-id="50dbc-304">License administrator</span></span> | <span data-ttu-id="50dbc-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="50dbc-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="50dbc-306">Lector de privacidad del Centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="50dbc-306">Message center privacy reader</span></span> | <span data-ttu-id="50dbc-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="50dbc-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="50dbc-308">Lector del centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="50dbc-308">Message center reader</span></span> | <span data-ttu-id="50dbc-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="50dbc-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="50dbc-310">Administrador de aplicaciones de Office</span><span class="sxs-lookup"><span data-stu-id="50dbc-310">Office apps administrator</span></span> | <span data-ttu-id="50dbc-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="50dbc-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="50dbc-312">Administrador de contraseñas</span><span class="sxs-lookup"><span data-stu-id="50dbc-312">Password administrator</span></span> | <span data-ttu-id="50dbc-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="50dbc-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="50dbc-314">Administrador de Power BI</span><span class="sxs-lookup"><span data-stu-id="50dbc-314">Power BI administrator</span></span> | <span data-ttu-id="50dbc-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="50dbc-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="50dbc-316">Administrador de Power Platform</span><span class="sxs-lookup"><span data-stu-id="50dbc-316">Power platform administrator</span></span> | <span data-ttu-id="50dbc-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="50dbc-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="50dbc-318">Administrador de autenticación con privilegios</span><span class="sxs-lookup"><span data-stu-id="50dbc-318">Privileged authentication administrator</span></span> | <span data-ttu-id="50dbc-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="50dbc-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="50dbc-320">Administrador de roles con privilegios</span><span class="sxs-lookup"><span data-stu-id="50dbc-320">Privileged role administrator</span></span> | <span data-ttu-id="50dbc-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="50dbc-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="50dbc-322">Lector de informes</span><span class="sxs-lookup"><span data-stu-id="50dbc-322">Reports reader</span></span> | <span data-ttu-id="50dbc-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="50dbc-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="50dbc-324">Administrador de búsqueda</span><span class="sxs-lookup"><span data-stu-id="50dbc-324">Search administrator</span></span> | <span data-ttu-id="50dbc-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="50dbc-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="50dbc-326">Editor de búsqueda</span><span class="sxs-lookup"><span data-stu-id="50dbc-326">Search editor</span></span> | <span data-ttu-id="50dbc-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="50dbc-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="50dbc-328">Administrador de seguridad</span><span class="sxs-lookup"><span data-stu-id="50dbc-328">Security administrator</span></span> | <span data-ttu-id="50dbc-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="50dbc-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="50dbc-330">Operador de seguridad</span><span class="sxs-lookup"><span data-stu-id="50dbc-330">Security operator</span></span> | <span data-ttu-id="50dbc-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="50dbc-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="50dbc-332">Lector de seguridad</span><span class="sxs-lookup"><span data-stu-id="50dbc-332">Security reader</span></span> | <span data-ttu-id="50dbc-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="50dbc-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="50dbc-334">Administrador del soporte técnico del servicio</span><span class="sxs-lookup"><span data-stu-id="50dbc-334">Service support administrator</span></span> | <span data-ttu-id="50dbc-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="50dbc-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="50dbc-336">Administrador de SharePoint</span><span class="sxs-lookup"><span data-stu-id="50dbc-336">SharePoint administrator</span></span> | <span data-ttu-id="50dbc-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="50dbc-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="50dbc-338">Administrador de Skype Empresarial</span><span class="sxs-lookup"><span data-stu-id="50dbc-338">Skype for Business administrator</span></span> | <span data-ttu-id="50dbc-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="50dbc-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="50dbc-340">Administrador de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="50dbc-340">Teams Communications Administrator</span></span> | <span data-ttu-id="50dbc-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="50dbc-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="50dbc-342">Ingeniero de soporte técnico de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="50dbc-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="50dbc-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="50dbc-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="50dbc-344">Especialista de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="50dbc-344">Teams Communications Specialist</span></span> | <span data-ttu-id="50dbc-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="50dbc-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="50dbc-346">Administrador de servicios de Teams</span><span class="sxs-lookup"><span data-stu-id="50dbc-346">Teams Service Administrator</span></span> | <span data-ttu-id="50dbc-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="50dbc-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="50dbc-348">Administrador de usuarios</span><span class="sxs-lookup"><span data-stu-id="50dbc-348">User administrator</span></span> | <span data-ttu-id="50dbc-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="50dbc-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50dbc-350">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="50dbc-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
