---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: bd0a7c5a905ae4888ea6ad326be5b16cbfaa10fc
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130410"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="a9ec5-103">Grupos de Azure AD, roles administrativos y roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a9ec5-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="a9ec5-104">Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a9ec5-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a9ec5-105">Azure Active Directory (AAD) proporciona diversos métodos de autorización que se pueden combinar con Identity de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a9ec5-106">Grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a9ec5-106">User-defined groups</span></span>
  * <span data-ttu-id="a9ec5-107">Seguridad</span><span class="sxs-lookup"><span data-stu-id="a9ec5-107">Security</span></span>
  * <span data-ttu-id="a9ec5-108">O365</span><span class="sxs-lookup"><span data-stu-id="a9ec5-108">O365</span></span>
  * <span data-ttu-id="a9ec5-109">Distribución</span><span class="sxs-lookup"><span data-stu-id="a9ec5-109">Distribution</span></span>
* <span data-ttu-id="a9ec5-110">Roles</span><span class="sxs-lookup"><span data-stu-id="a9ec5-110">Roles</span></span>
  * <span data-ttu-id="a9ec5-111">Roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="a9ec5-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="a9ec5-112">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a9ec5-112">User-defined roles</span></span>

<span data-ttu-id="a9ec5-113">Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="a9ec5-114">Independiente con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="a9ec5-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a9ec5-115">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="a9ec5-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a9ec5-116">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="a9ec5-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="a9ec5-117">Permisos para Microsoft Graph API</span><span class="sxs-lookup"><span data-stu-id="a9ec5-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="a9ec5-118">Se requiere una llamada a [Microsoft Graph API](/graph/use-the-api) para cualquier usuario de la aplicación con más de cinco pertenencias a grupos de seguridad y roles de administrador de AAD integrados.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="a9ec5-119">Para permitir llamadas a Graph API, proporcione a la aplicación cliente o independiente de una solución de Blazor hospedada cualquiera de los siguientes [permisos de Graph API](/graph/permissions-reference) en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="a9ec5-120">`Directory.Read.All` es el permiso con privilegios mínimos y es el que se usa para el ejemplo que se describe en este artículo.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="a9ec5-121">Grupos definidos por el usuario y roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="a9ec5-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="a9ec5-122">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia de tipo `groups`, vea los siguientes artículos sobre Azure.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="a9ec5-123">Asigne usuarios a grupos de AAD definidos por el usuario y a roles administrativos integrados.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="a9ec5-124">Roles que usan grupos de seguridad de Azure AD</span><span class="sxs-lookup"><span data-stu-id="a9ec5-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="a9ec5-125">Atributo `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="a9ec5-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="a9ec5-126">En los siguientes ejemplos se da por hecho que un usuario está asignado al rol integrado de AAD *Administrador de facturación*.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="a9ec5-127">La notificación `groups` única enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="a9ec5-128">La aplicación debe convertir esta matriz JSON de grupos y roles en notificaciones `group` individuales, a partir de las cuales la aplicación podrá crear [directivas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="a9ec5-129">Cuando el número de roles administrativos y grupos definidos por el usuario de Azure integrados es superior a cinco, AAD envía una notificación `hasgroups` con un valor `true` en lugar de enviar una notificación `groups`.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="a9ec5-130">Cualquier aplicación que pueda tener más de cinco roles y grupos asignados a sus usuarios debe realizar una llamada a Graph API independiente para obtener los roles y grupos de un usuario.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="a9ec5-131">En la implementación de ejemplo que se proporciona en este artículo se aborda este escenario.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="a9ec5-132">Para más información, vea la información sobre las notificaciones `groups` y `hasgroups` en el artículo [Tokens de acceso de la Plataforma de identidad de Microsoft: Notificaciones de carga](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="a9ec5-133">Extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz de grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="a9ec5-134">Asigne una matriz vacía a cada propiedad para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach` más adelante.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="a9ec5-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="a9ec5-136">En la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, cree una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="a9ec5-137">Utilice el ámbito correcto (permiso) para las llamadas a Graph API que obtienen información de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="a9ec5-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="a9ec5-139">En `Program.Main` (`Program.cs`), agregue el servicio de implementación <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> y agregue un elemento con nombre <xref:System.Net.Http.HttpClient> para realizar solicitudes a Graph API.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="a9ec5-140">En el siguiente ejemplo se nombra el cliente `GraphAPI`:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="a9ec5-141">Cree clases de objetos de directorio de AAD para recibir los roles y grupos de Open Data Protocol (OData) desde una llamada a Graph API.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="a9ec5-142">OData se presenta en formato JSON, y una llamada a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> rellena una instancia de la clase `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="a9ec5-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="a9ec5-144">Cree una fábrica de usuario personalizada para procesar notificaciones de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="a9ec5-145">En la implementación de ejemplo siguiente también se controla la matriz de notificaciones `roles`, que se describe en la sección [Roles definidos por el usuario](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a9ec5-146">Si la notificación `hasgroups` está presente, el elemento <xref:System.Net.Http.HttpClient> con nombre se usa para hacer una solicitud autorizada a Graph API para obtener los roles y grupos del usuario.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="a9ec5-147">Esta implementación usa el punto de conexión de la plataforma de Identity de Microsoft v1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentación de API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="a9ec5-148">Las instrucciones de este tema se actualizarán para Identity v2.0 cuando se actualicen los paquetes MSAL para v2.0.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="a9ec5-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="a9ec5-150">No es necesario incluir código para quitar la notificación `groups` original, si está presente, ya que el marco de trabajo la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="a9ec5-151">El enfoque en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="a9ec5-152">Agrega una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada para adjuntar tokens de acceso a solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="a9ec5-153">Agrega un elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes de API web a un punto de conexión de API web externo seguro.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="a9ec5-154">Utiliza el elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes autorizadas.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="a9ec5-155">La cobertura general de este enfoque se encuentra en el artículo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="a9ec5-156">Registre la fábrica en el archivo `Program.Main` (`Program.cs`) de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="a9ec5-157">Dé el consentimiento en el ámbito de permiso de `Directory.Read.All` como un ámbito adicional para la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="a9ec5-158">Cree una [directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="a9ec5-159">En el siguiente ejemplo se crea una directiva para el rol integrado *Administrador de facturación* de AAD:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a9ec5-160">Para obtener una lista completa de los identificadores de objeto de rol de AAD, vea la sección [Identificadores de grupos de roles de AAD](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="a9ec5-161">En los siguientes ejemplos, la aplicación usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="a9ec5-162">El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="a9ec5-163">El acceso a un componente completo se puede basar en la directiva usando la [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="a9ec5-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="a9ec5-164">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="a9ec5-165">También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="a9ec5-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="a9ec5-166">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a9ec5-166">User-defined roles</span></span>

<span data-ttu-id="a9ec5-167">Una aplicación registrada en AAD también se puede configurar para usar roles definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="a9ec5-168">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia `roles`, vea [Procedimiento para agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="a9ec5-169">En el siguiente ejemplo se da por hecho que una aplicación está configurada con dos roles:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="a9ec5-170">Aunque no se pueden asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, sí se pueden asignar usuarios a roles y recibir una notificación `roles` relativa a los usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="a9ec5-171">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="a9ec5-172">Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="a9ec5-173">La notificación `roles` única enviada por AAD presenta los roles definidos por el usuario como valores `value` de `appRoles` en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="a9ec5-174">La aplicación debe convertir esta matriz JSON de roles en notificaciones `role` individuales.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="a9ec5-175">Los elementos `CustomUserFactory` que se muestran en la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles) están configurados para actuar a partir de una notificación `roles` con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="a9ec5-176">Agregue y registre el elemento `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, tal y como se explica en la sección [Grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="a9ec5-177">No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="a9ec5-178">En el archivo `Program.Main` de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, especifique la notificación denominada "`role`" como la notificación de rol:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="a9ec5-179">Los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a9ec5-180">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="a9ec5-181">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="a9ec5-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a9ec5-182">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="a9ec5-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a9ec5-183">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="a9ec5-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a9ec5-184">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="a9ec5-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="a9ec5-185">Identificadores de grupos de roles administrativos de AAD</span><span class="sxs-lookup"><span data-stu-id="a9ec5-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="a9ec5-186">Los identificadores de objeto reunidos en la siguiente tabla sirven para crear [directivas](xref:security/authorization/policies) para notificaciones `group`.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="a9ec5-187">Las directivas permiten a una aplicación autorizar a usuarios para que realicen diversas actividades en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9ec5-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="a9ec5-188">Para más información, vea la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="a9ec5-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="a9ec5-189">Rol administrativo de AAD</span><span class="sxs-lookup"><span data-stu-id="a9ec5-189">AAD Administrative Role</span></span> | <span data-ttu-id="a9ec5-190">Id. de objeto</span><span class="sxs-lookup"><span data-stu-id="a9ec5-190">Object ID</span></span>
--- | ---
<span data-ttu-id="a9ec5-191">Administrador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="a9ec5-191">Application administrator</span></span> | <span data-ttu-id="a9ec5-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="a9ec5-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="a9ec5-193">Desarrollador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="a9ec5-193">Application developer</span></span> | <span data-ttu-id="a9ec5-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="a9ec5-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="a9ec5-195">Administrador de autenticación</span><span class="sxs-lookup"><span data-stu-id="a9ec5-195">Authentication administrator</span></span> | <span data-ttu-id="a9ec5-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="a9ec5-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="a9ec5-197">Administrador de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a9ec5-197">Azure DevOps administrator</span></span> | <span data-ttu-id="a9ec5-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="a9ec5-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="a9ec5-199">Administrador de Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="a9ec5-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="a9ec5-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="a9ec5-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="a9ec5-201">Administrador de conjuntos de claves B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="a9ec5-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="a9ec5-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="a9ec5-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="a9ec5-203">Administrador de directivas B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="a9ec5-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="a9ec5-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="a9ec5-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="a9ec5-205">Administrador de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="a9ec5-205">B2C user flow administrator</span></span> | <span data-ttu-id="a9ec5-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="a9ec5-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="a9ec5-207">Administrador de atributos de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="a9ec5-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="a9ec5-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="a9ec5-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="a9ec5-209">Administrador de facturación</span><span class="sxs-lookup"><span data-stu-id="a9ec5-209">Billing administrator</span></span> | <span data-ttu-id="a9ec5-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="a9ec5-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="a9ec5-211">Administrador de aplicaciones en la nube</span><span class="sxs-lookup"><span data-stu-id="a9ec5-211">Cloud application administrator</span></span> | <span data-ttu-id="a9ec5-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="a9ec5-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="a9ec5-213">Administrador de dispositivos en la nube</span><span class="sxs-lookup"><span data-stu-id="a9ec5-213">Cloud device administrator</span></span> | <span data-ttu-id="a9ec5-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="a9ec5-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="a9ec5-215">Administrador de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="a9ec5-215">Compliance administrator</span></span> | <span data-ttu-id="a9ec5-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="a9ec5-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="a9ec5-217">Administrador de datos de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="a9ec5-217">Compliance data administrator</span></span> | <span data-ttu-id="a9ec5-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="a9ec5-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="a9ec5-219">Administrador de acceso condicional</span><span class="sxs-lookup"><span data-stu-id="a9ec5-219">Conditional Access administrator</span></span> | <span data-ttu-id="a9ec5-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="a9ec5-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="a9ec5-221">Aprobador del acceso a la Caja de seguridad del cliente</span><span class="sxs-lookup"><span data-stu-id="a9ec5-221">Customer LockBox access approver</span></span> | <span data-ttu-id="a9ec5-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="a9ec5-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="a9ec5-223">Administrador de Análisis de escritorio</span><span class="sxs-lookup"><span data-stu-id="a9ec5-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="a9ec5-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="a9ec5-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="a9ec5-225">Lectores de directorios</span><span class="sxs-lookup"><span data-stu-id="a9ec5-225">Directory readers</span></span> | <span data-ttu-id="a9ec5-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="a9ec5-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="a9ec5-227">Administrador de Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="a9ec5-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="a9ec5-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="a9ec5-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="a9ec5-229">Administrador de Exchange</span><span class="sxs-lookup"><span data-stu-id="a9ec5-229">Exchange administrator</span></span> | <span data-ttu-id="a9ec5-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="a9ec5-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="a9ec5-231">Administrador de proveedor de Identity externo</span><span class="sxs-lookup"><span data-stu-id="a9ec5-231">External Identity Provider administrator</span></span> | <span data-ttu-id="a9ec5-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="a9ec5-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="a9ec5-233">Administrador global</span><span class="sxs-lookup"><span data-stu-id="a9ec5-233">Global administrator</span></span> | <span data-ttu-id="a9ec5-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="a9ec5-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="a9ec5-235">Lector global</span><span class="sxs-lookup"><span data-stu-id="a9ec5-235">Global reader</span></span> | <span data-ttu-id="a9ec5-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="a9ec5-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="a9ec5-237">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="a9ec5-237">Groups administrator</span></span> | <span data-ttu-id="a9ec5-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="a9ec5-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="a9ec5-239">Invitador de usuarios</span><span class="sxs-lookup"><span data-stu-id="a9ec5-239">Guest inviter</span></span> | <span data-ttu-id="a9ec5-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="a9ec5-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="a9ec5-241">Administrador del departamento de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="a9ec5-241">Helpdesk administrator</span></span> | <span data-ttu-id="a9ec5-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="a9ec5-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="a9ec5-243">Administrador de Intune</span><span class="sxs-lookup"><span data-stu-id="a9ec5-243">Intune administrator</span></span> | <span data-ttu-id="a9ec5-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="a9ec5-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="a9ec5-245">Administrador de Kaizala</span><span class="sxs-lookup"><span data-stu-id="a9ec5-245">Kaizala administrator</span></span> | <span data-ttu-id="a9ec5-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="a9ec5-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="a9ec5-247">Administrador de licencias</span><span class="sxs-lookup"><span data-stu-id="a9ec5-247">License administrator</span></span> | <span data-ttu-id="a9ec5-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="a9ec5-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="a9ec5-249">Lector de privacidad del Centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="a9ec5-249">Message center privacy reader</span></span> | <span data-ttu-id="a9ec5-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="a9ec5-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="a9ec5-251">Lector del centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="a9ec5-251">Message center reader</span></span> | <span data-ttu-id="a9ec5-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="a9ec5-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="a9ec5-253">Administrador de aplicaciones de Office</span><span class="sxs-lookup"><span data-stu-id="a9ec5-253">Office apps administrator</span></span> | <span data-ttu-id="a9ec5-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="a9ec5-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="a9ec5-255">Administrador de contraseñas</span><span class="sxs-lookup"><span data-stu-id="a9ec5-255">Password administrator</span></span> | <span data-ttu-id="a9ec5-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="a9ec5-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="a9ec5-257">Administrador de Power BI</span><span class="sxs-lookup"><span data-stu-id="a9ec5-257">Power BI administrator</span></span> | <span data-ttu-id="a9ec5-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="a9ec5-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="a9ec5-259">Administrador de Power Platform</span><span class="sxs-lookup"><span data-stu-id="a9ec5-259">Power platform administrator</span></span> | <span data-ttu-id="a9ec5-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="a9ec5-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="a9ec5-261">Administrador de autenticación con privilegios</span><span class="sxs-lookup"><span data-stu-id="a9ec5-261">Privileged authentication administrator</span></span> | <span data-ttu-id="a9ec5-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="a9ec5-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="a9ec5-263">Administrador de roles con privilegios</span><span class="sxs-lookup"><span data-stu-id="a9ec5-263">Privileged role administrator</span></span> | <span data-ttu-id="a9ec5-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="a9ec5-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="a9ec5-265">Lector de informes</span><span class="sxs-lookup"><span data-stu-id="a9ec5-265">Reports reader</span></span> | <span data-ttu-id="a9ec5-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="a9ec5-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="a9ec5-267">Administrador de búsqueda</span><span class="sxs-lookup"><span data-stu-id="a9ec5-267">Search administrator</span></span> | <span data-ttu-id="a9ec5-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="a9ec5-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="a9ec5-269">Editor de búsqueda</span><span class="sxs-lookup"><span data-stu-id="a9ec5-269">Search editor</span></span> | <span data-ttu-id="a9ec5-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="a9ec5-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="a9ec5-271">Administrador de seguridad</span><span class="sxs-lookup"><span data-stu-id="a9ec5-271">Security administrator</span></span> | <span data-ttu-id="a9ec5-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="a9ec5-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="a9ec5-273">Operador de seguridad</span><span class="sxs-lookup"><span data-stu-id="a9ec5-273">Security operator</span></span> | <span data-ttu-id="a9ec5-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="a9ec5-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="a9ec5-275">Lector de seguridad</span><span class="sxs-lookup"><span data-stu-id="a9ec5-275">Security reader</span></span> | <span data-ttu-id="a9ec5-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="a9ec5-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="a9ec5-277">Administrador del soporte técnico del servicio</span><span class="sxs-lookup"><span data-stu-id="a9ec5-277">Service support administrator</span></span> | <span data-ttu-id="a9ec5-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="a9ec5-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="a9ec5-279">Administrador de SharePoint</span><span class="sxs-lookup"><span data-stu-id="a9ec5-279">SharePoint administrator</span></span> | <span data-ttu-id="a9ec5-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="a9ec5-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="a9ec5-281">Administrador de Skype Empresarial</span><span class="sxs-lookup"><span data-stu-id="a9ec5-281">Skype for Business administrator</span></span> | <span data-ttu-id="a9ec5-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="a9ec5-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="a9ec5-283">Administrador de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a9ec5-283">Teams Communications Administrator</span></span> | <span data-ttu-id="a9ec5-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="a9ec5-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="a9ec5-285">Ingeniero de soporte técnico de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a9ec5-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="a9ec5-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="a9ec5-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="a9ec5-287">Especialista de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a9ec5-287">Teams Communications Specialist</span></span> | <span data-ttu-id="a9ec5-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="a9ec5-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="a9ec5-289">Administrador de servicios de Teams</span><span class="sxs-lookup"><span data-stu-id="a9ec5-289">Teams Service Administrator</span></span> | <span data-ttu-id="a9ec5-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="a9ec5-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="a9ec5-291">Administrador de usuarios</span><span class="sxs-lookup"><span data-stu-id="a9ec5-291">User administrator</span></span> | <span data-ttu-id="a9ec5-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="a9ec5-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9ec5-293">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a9ec5-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
