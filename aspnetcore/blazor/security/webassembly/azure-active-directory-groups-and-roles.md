---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94981887"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="a2cbe-103">Grupos, roles de administrador y roles definidos por el usuario de Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="a2cbe-104">Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="a2cbe-105">Este artículo se aplica a las aplicaciones de ASP.NET Core de Blazor, versión 3.1, con Microsoft Identity v 1.0, y está programada su actualización a 5.0 con Identity v2.0.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity v1.0 and is scheduled for update to 5.0 with Identity v2.0.</span></span> <span data-ttu-id="a2cbe-106">Para obtener más información, vea [Blazor WASM con grupos y roles de AAD/B2C (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-106">For more information, see [Blazor WASM with AAD/B2C groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span></span>

<span data-ttu-id="a2cbe-107">Azure Active Directory (AAD) proporciona diversos enfoques de autorización que se pueden combinar con ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-107">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a2cbe-108">Grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a2cbe-108">User-defined groups</span></span>
  * <span data-ttu-id="a2cbe-109">Seguridad</span><span class="sxs-lookup"><span data-stu-id="a2cbe-109">Security</span></span>
  * <span data-ttu-id="a2cbe-110">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="a2cbe-110">Microsoft 365</span></span>
  * <span data-ttu-id="a2cbe-111">Distribución</span><span class="sxs-lookup"><span data-stu-id="a2cbe-111">Distribution</span></span>
* <span data-ttu-id="a2cbe-112">Roles</span><span class="sxs-lookup"><span data-stu-id="a2cbe-112">Roles</span></span>
  * <span data-ttu-id="a2cbe-113">Roles de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-113">AAD Administrator Roles</span></span>
  * <span data-ttu-id="a2cbe-114">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a2cbe-114">User-defined roles</span></span>

<span data-ttu-id="a2cbe-115">Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-115">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="a2cbe-116">Independiente con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="a2cbe-116">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a2cbe-117">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-117">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a2cbe-118">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-118">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="a2cbe-119">Ámbitos</span><span class="sxs-lookup"><span data-stu-id="a2cbe-119">Scopes</span></span>

<span data-ttu-id="a2cbe-120">Para los usuarios de aplicaciones con más de cinco pertenencias a grupos de seguridad y roles de administrador de AAD, se requiere una llamada [Microsoft Graph API](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-120">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="a2cbe-121">Para permitir llamadas de Graph API, proporcione a la aplicación independiente o *`Client`* de una solución de Blazor hospedada cualquiera de los siguientes [permisos de Graph API (ámbitos)](/graph/permissions-reference) en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-121">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="a2cbe-122">`Directory.Read.All` es el ámbito con menos privilegios y es el que se usa en el ejemplo que se describe en este artículo.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-122">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a2cbe-123">Roles de administrador y grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a2cbe-123">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a2cbe-124">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia de tipo `groups`, vea los siguientes artículos sobre Azure.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-124">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="a2cbe-125">Asigne usuarios a grupos definidos por el usuario y roles de administrador de AAD.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-125">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="a2cbe-126">Roles que usan grupos de seguridad de Azure AD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-126">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="a2cbe-127">Atributo `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="a2cbe-127">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="a2cbe-128">En los siguientes ejemplos se da por hecho que a un usuario se le asigna el rol *Administrador de facturación*.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-128">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="a2cbe-129">La notificación `groups` única enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-129">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="a2cbe-130">La aplicación debe convertir esta matriz JSON de grupos y roles en notificaciones `group` individuales, a partir de las cuales la aplicación podrá crear [directivas](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-130">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="a2cbe-131">Cuando el número de roles de administrador y grupos definidos por el usuario de AAD asignados es superior a cinco, AAD envía una notificación `hasgroups` con un valor `true` en lugar de enviar una notificación `groups`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-131">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="a2cbe-132">Cualquier aplicación que pueda tener más de cinco roles y grupos asignados a sus usuarios debe realizar una llamada a Graph API independiente para obtener los roles y grupos de un usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-132">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="a2cbe-133">En la implementación de ejemplo que se proporciona en este artículo se aborda este escenario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-133">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="a2cbe-134">Para más información, vea la información sobre las notificaciones `groups` y `hasgroups` en el artículo [Tokens de acceso de la Plataforma de identidad de Microsoft: Notificaciones de carga](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-134">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="a2cbe-135">Extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz de grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-135">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="a2cbe-136">Asigne una matriz vacía a cada propiedad para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach` más adelante.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-136">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="a2cbe-137">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-137">`CustomUserAccount.cs`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a2cbe-138">Use **cualquiera** de los enfoques siguientes para crear notificaciones de grupos y roles de AAD:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-138">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="a2cbe-139">Uso del SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="a2cbe-139">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="a2cbe-140">Uso de una instancia de `HttpClient` con nombre</span><span class="sxs-lookup"><span data-stu-id="a2cbe-140">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="a2cbe-141">Uso del SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="a2cbe-141">Use the Graph SDK</span></span>

<span data-ttu-id="a2cbe-142">Agregue una referencia de paquete a la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada para [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-142">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="a2cbe-143">Agregue la configuración y las clases de utilidad del SDK de Graph de la sección *SDK de Graph* del artículo <xref:blazor/security/webassembly/graph-api#graph-sdk>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-143">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="a2cbe-144">Agregue la siguiente fábrica de cuentas de usuario personalizada a la aplicación independiente o *`Client`* de una solución de Blazor hospedada (`CustomAccountFactory.cs`).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-144">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="a2cbe-145">La fábrica de usuarios personalizada se usa para procesar notificaciones de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-145">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="a2cbe-146">La matriz de notificaciones de `roles` se trata en la sección [Roles definidos por el usuario](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-146">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a2cbe-147">Si la notificación `hasgroups` está presente, se usa el SDK de Graph para realizar una solicitud autorizada a Graph API a fin de obtener los roles y grupos del usuario:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-147">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
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

<span data-ttu-id="a2cbe-148">El código anterior no incluye pertenencias transitivas.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-148">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="a2cbe-149">Si la aplicación requiere notificaciones directas y transitivas de pertenencia a grupos:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-149">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="a2cbe-150">Cambie el tipo `IUserMemberOfCollectionWithReferencesPage` de `groupsAndAzureRoles` a `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-150">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="a2cbe-151">Al solicitar los grupos y roles del usuario, reemplace la propiedad `MemberOf` por `TransitiveMemberOf`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-151">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="a2cbe-152">En `Program.Main` (`Program.cs`), configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada: Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-152">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="a2cbe-153">Uso de una instancia de `HttpClient` con nombre</span><span class="sxs-lookup"><span data-stu-id="a2cbe-153">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="a2cbe-154">En la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada, cree una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-154">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="a2cbe-155">Use el ámbito correcto para las llamadas de Graph API que obtienen información de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-155">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="a2cbe-156">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-156">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="a2cbe-157">En `Program.Main` (`Program.cs`), agregue el servicio de implementación <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> y agregue un elemento con nombre <xref:System.Net.Http.HttpClient> para realizar solicitudes a Graph API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-157">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="a2cbe-158">En el siguiente ejemplo se nombra el cliente `GraphAPI`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-158">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="a2cbe-159">Cree clases de objetos de directorio de AAD para recibir los roles y grupos de Open Data Protocol (OData) desde una llamada a Graph API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-159">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="a2cbe-160">OData se presenta en formato JSON, y una llamada a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> rellena una instancia de la clase `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-160">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="a2cbe-161">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-161">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="a2cbe-162">Cree una fábrica de usuario personalizada para procesar notificaciones de roles y grupos.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-162">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="a2cbe-163">En la implementación de ejemplo siguiente también se controla la matriz de notificaciones `roles`, que se describe en la sección [Roles definidos por el usuario](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-163">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a2cbe-164">Si la notificación `hasgroups` está presente, el elemento <xref:System.Net.Http.HttpClient> con nombre se usa para hacer una solicitud autorizada a Graph API para obtener los roles y grupos del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-164">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="a2cbe-165">Esta implementación usa el punto de conexión de la plataforma de Identity de Microsoft v1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentación de API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-165">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="a2cbe-166">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-166">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {Message}", 
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

<span data-ttu-id="a2cbe-167">No es necesario incluir código para quitar la notificación `groups` original, si está presente, ya que el marco de trabajo la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-167">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="a2cbe-168">El enfoque en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-168">The approach in this example:</span></span>
>
> * <span data-ttu-id="a2cbe-169">Agrega una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada para adjuntar tokens de acceso a solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-169">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="a2cbe-170">Agrega un elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes de API web a un punto de conexión de API web externo seguro.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-170">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="a2cbe-171">Utiliza el elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes autorizadas.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-171">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="a2cbe-172">La cobertura general de este enfoque se encuentra en el artículo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-172">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="a2cbe-173">Registre la fábrica en el archivo `Program.Main` (`Program.cs`) de la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-173">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="a2cbe-174">Dé el consentimiento al ámbito `Directory.Read.All` como un ámbito adicional para la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-174">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

## <a name="authorization-configuration"></a><span data-ttu-id="a2cbe-175">Configuración de autorización</span><span class="sxs-lookup"><span data-stu-id="a2cbe-175">Authorization configuration</span></span>

<span data-ttu-id="a2cbe-176">Cree una [directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-176">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="a2cbe-177">En el siguiente ejemplo se crea una directiva para el rol *Administrador de facturación* de AAD:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-177">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a2cbe-178">Para ver una lista completa de identificadores de objeto de rol de AAD, consulte la sección [Identificadores de objeto de rol de administrador de AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-178">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="a2cbe-179">En los siguientes ejemplos, la aplicación usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-179">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="a2cbe-180">El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-180">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="a2cbe-181">El acceso a un componente completo se puede basar en la directiva usando la [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="a2cbe-181">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="a2cbe-182">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-182">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="a2cbe-183">También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="a2cbe-183">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a2cbe-184">Autorización de acceso de la API de servidor para roles de administrador y grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a2cbe-184">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a2cbe-185">Además de autorizar a los usuarios de la aplicación WebAssembly del lado cliente el acceso a páginas y recursos, la API de servidor puede autorizar a los usuarios el acceso a puntos de conexión de API seguros.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-185">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="a2cbe-186">Una vez que la aplicación *Server* valida el token de acceso del usuario:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-186">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="a2cbe-187">La aplicación de API de servidor usa la [notificación de identificador de objeto(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) inmutable del usuario del token de acceso para obtener un token de acceso para Graph API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-187">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="a2cbe-188">Una llamada de Graph API obtiene el grupo de seguridad definido por el usuario y las pertenencias a roles de administrador de Azure del usuario mediante una llamada a [`memberOf`](/graph/api/user-list-memberof) en el usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-188">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="a2cbe-189">Las pertenencias se usan para establecer notificaciones `group` .</span><span class="sxs-lookup"><span data-stu-id="a2cbe-189">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="a2cbe-190">Se pueden usar [directivas de autorización](xref:security/authorization/policies) para limitar el acceso de los usuarios a los puntos de conexión de API de servidor por medio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-190">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a2cbe-191">Esta guía no incluye actualmente la autorización de usuarios en función de sus [roles definidos por el usuario de AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-191">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="a2cbe-192">En las instrucciones de esta sección se configura la aplicación de API de servidor como una [*aplicación de demonio*](/azure/active-directory/develop/scenario-daemon-overview) para la llamada de Microsoft Graph API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-192">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="a2cbe-193">Este enfoque **no**:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-193">This approach does **not**:</span></span>

* <span data-ttu-id="a2cbe-194">Requiere el ámbito `access_as_user`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-194">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="a2cbe-195">Accede a Graph API en nombre del usuario o cliente que realiza la solicitud de API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-195">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="a2cbe-196">La llamada a Graph API por parte de la aplicación de API de servidor solo requiere un ámbito de Graph API **Application** de aplicación de API de servidor en `Directory.Read.All` en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-196">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="a2cbe-197">Este enfoque evita totalmente que la aplicación cliente acceda a los datos del directorio que la API de servidor no permite explícitamente.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-197">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="a2cbe-198">La aplicación cliente solo puede acceder a los puntos de conexión del controlador de la aplicación de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-198">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="a2cbe-199">Configuración de Azure</span><span class="sxs-lookup"><span data-stu-id="a2cbe-199">Azure configuration</span></span>

* <span data-ttu-id="a2cbe-200">Confirme que al registro de aplicación *Server* se le proporciona ámbito de Graph API **Application** (no **Delegated**) en `Directory.Read.All`, que es el nivel de acceso con menos privilegios de los grupos de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-200">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="a2cbe-201">Confirme que se aplica consentimiento del administrador al ámbito después de realizar la asignación de ámbito.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-201">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="a2cbe-202">Asigne un nuevo secreto de cliente a la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-202">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="a2cbe-203">Fíjese en el secreto de la configuración de la aplicación de la sección [Configuración de la aplicación](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-203">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="a2cbe-204">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="a2cbe-204">App settings</span></span>

<span data-ttu-id="a2cbe-205">En el archivo de configuración de la aplicación (`appsettings.json` o `appsettings.Production.json`), cree una entrada `ClientSecret` con el secreto de cliente de la aplicación *Server* desde Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-205">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="a2cbe-206">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-206">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a2cbe-207">Si no se comprueba el dominio del publicador de inquilinos, el ámbito de API de servidor para el acceso de usuarios o clientes usa un URI basado en `https://`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-207">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="a2cbe-208">En este escenario, la aplicación de API de servidor requiere configuración `Audience` en el archivo `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-208">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="a2cbe-209">En la siguiente configuración, el final del valor `Audience` **no** incluye el ámbito predeterminado `/{DEFAULT SCOPE}`, donde el marcador de posición `{DEFAULT SCOPE}` es el ámbito predeterminado:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-209">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="a2cbe-210">En la configuración de ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-210">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="a2cbe-211">El dominio del inquilino es `contoso.onmicrosoft.com`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-211">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="a2cbe-212">La aplicación de API de servidor `ClientId` es `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-212">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="a2cbe-213">La configuración de `Audience` de forma explícita normalmente **no** es necesaria para aplicaciones con un dominio de publicador comprobado que tiene un ámbito de API basado en `api://`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-213">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="a2cbe-214">Para obtener más información, vea <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-214">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="a2cbe-215">Directivas de autorización</span><span class="sxs-lookup"><span data-stu-id="a2cbe-215">Authorization policies</span></span>

<span data-ttu-id="a2cbe-216">Cree [directivas de autorización](xref:security/authorization/policies) para los grupos de seguridad y los roles de administrador de AAD en el elemento `Startup.ConfigureServices` de la aplicación *Server*(`Startup.cs`) en función de los identificadores de objeto de grupo y de los [identificadores de objeto de rol de administrador de AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-216">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="a2cbe-217">Por ejemplo, una directiva de rol de administrador de facturación de Azure tiene la siguiente configuración:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-217">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a2cbe-218">Para más información, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-218">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="a2cbe-219">Acceso del controlador</span><span class="sxs-lookup"><span data-stu-id="a2cbe-219">Controller access</span></span>

<span data-ttu-id="a2cbe-220">Exija directivas en los controladores de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-220">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="a2cbe-221">En el siguiente ejemplo se limita el acceso a los datos de facturación del elemento `BillingDataController` a los administradores de facturación de Azure con un nombre de directiva de `BillingAdmin`, según se ha configurado en la sección [Directivas de autorización](#authorization-policies):</span><span class="sxs-lookup"><span data-stu-id="a2cbe-221">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a2cbe-222">Paquetes</span><span class="sxs-lookup"><span data-stu-id="a2cbe-222">Packages</span></span>

<span data-ttu-id="a2cbe-223">Agregue referencias de paquete a la aplicación *Server* para los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-223">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a2cbe-224">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a2cbe-224">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a2cbe-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="a2cbe-226">Servicios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-226">Services</span></span>

<span data-ttu-id="a2cbe-227">En el método `Startup.ConfigureServices` de la aplicación *Server* se requieren espacios de nombres adicionales para el código de la clase `Startup` de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-227">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a2cbe-228">Agregue los siguientes espacios de nombres a `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-228">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="a2cbe-229">Al configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-229">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a2cbe-230">Tiene la opción de incluir el procesamiento de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-230">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a2cbe-231">Por ejemplo, la aplicación puede registrar un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-231">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a2cbe-232">En <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, realice una llamada Graph API para obtener los grupos y los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-232">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a2cbe-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> proporciona información de identificación personal (PII) en los mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a2cbe-234">Active PII únicamente para la depuración con cuentas de usuario de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-234">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a2cbe-235">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-235">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="a2cbe-236">En el código anterior, el control de los siguientes errores de token es opcional:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-236">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="a2cbe-237">`MsalUiRequiredException`: la aplicación no tiene permisos suficientes (ámbitos).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-237">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="a2cbe-238">Determine si los ámbitos de aplicación de API de servidor en Azure Portal incluyen un permiso **Application** en `Directory.Read.All`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-238">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="a2cbe-239">Confirme que el administrador de inquilinos ha concedido permisos a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-239">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="a2cbe-240">`MsalServiceException` (`AADSTS70011`): confirme que el ámbito es `https://graph.microsoft.com/.default`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-240">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a2cbe-241">Paquetes</span><span class="sxs-lookup"><span data-stu-id="a2cbe-241">Packages</span></span>

<span data-ttu-id="a2cbe-242">Agregue referencias de paquete a la aplicación *Server* para los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-242">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a2cbe-243">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a2cbe-243">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a2cbe-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="a2cbe-245">Configuración del servicio</span><span class="sxs-lookup"><span data-stu-id="a2cbe-245">Service configuration</span></span>

<span data-ttu-id="a2cbe-246">En el método `Startup.ConfigureServices` de la aplicación *Server*, agregue lógica para realizar la llamada Graph API y establezca notificaciones `group` de usuario para los roles y grupos de seguridad del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-246">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="a2cbe-247">En el código de ejemplo de esta sección se usa el Biblioteca de autenticación de Active Directory (ADAL), que se basa en Microsoft Identity Platform v1.0.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-247">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="a2cbe-248">Se requieren espacios de nombres adicionales para el código de la clase `Startup` de la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-248">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a2cbe-249">El siguiente conjunto de instrucciones `using` incluye los espacios de nombres necesarios para el código que se muestra a continuación en esta sección:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-249">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="a2cbe-250">Al configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-250">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a2cbe-251">Tiene la opción de incluir el procesamiento de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-251">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a2cbe-252">Por ejemplo, la aplicación puede registrar un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-252">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a2cbe-253">En <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, realice una llamada Graph API para obtener los grupos y los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-253">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a2cbe-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> proporciona información de identificación personal (PII) en los mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a2cbe-255">Active PII únicamente para la depuración con cuentas de usuario de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-255">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a2cbe-256">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-256">In `Startup.ConfigureServices`:</span></span>

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="a2cbe-257">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-257">In the preceding example:</span></span>

* <span data-ttu-id="a2cbe-258">Primero se intenta la adquisición silenciosa del token (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) porque el token de acceso puede haberse almacenado ya en la caché de tokens de ADAL.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-258">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="a2cbe-259">Es más rápido obtener el token de la caché que solicitar uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-259">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="a2cbe-260">Si el token de acceso no se adquiere de la caché (se produce <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>), se realiza una aserción de usuario (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) con la credencial de cliente (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) para obtener el token en nombre del usuario (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-260">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="a2cbe-261">Luego, `Microsoft.Graph.GraphServiceClient` puede proceder a usar el token para realizar la llamada Graph API.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-261">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="a2cbe-262">El token se coloca en la caché de tokens de ADAL.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-262">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="a2cbe-263">En las llamadas Graph API futuras correspondientes al mismo usuario, el token se adquiere de la caché de forma silenciosa con <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-263">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="a2cbe-264">El código de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> no obtiene pertenencias transitivas.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-264">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="a2cbe-265">Para cambiar el código a fin de obtener pertenencias directas y transitivas:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-265">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="a2cbe-266">En la línea de código:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-266">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="a2cbe-267">Reemplace la línea anterior por:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-267">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="a2cbe-268">En la línea de código:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-268">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="a2cbe-269">Reemplace la línea anterior por:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-269">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="a2cbe-270">Al crear notificaciones, el código de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> no distingue entre grupos de seguridad y roles de administrador de AAD.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-270">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="a2cbe-271">Para que lo haga, compruebe el valor de `entry.ODataType` al recorrer en iteración los grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-271">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="a2cbe-272">Para crear notificaciones de rol y de grupo de seguridad por separado, use código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-272">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="a2cbe-273">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="a2cbe-273">User-defined roles</span></span>

<span data-ttu-id="a2cbe-274">Una aplicación registrada en AAD también se puede configurar para usar roles definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-274">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="a2cbe-275">Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia `roles`, vea [Procedimiento para agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-275">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="a2cbe-276">En el siguiente ejemplo se da por hecho que una aplicación está configurada con dos roles:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-276">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="a2cbe-277">Aunque no se pueden asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, sí se pueden asignar usuarios a roles y recibir una notificación `roles` relativa a los usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-277">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="a2cbe-278">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-278">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="a2cbe-279">Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-279">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="a2cbe-280">La notificación `roles` única enviada por AAD presenta los roles definidos por el usuario como valores `value` de `appRoles` en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-280">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="a2cbe-281">La aplicación debe convertir esta matriz JSON de roles en notificaciones `role` individuales.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-281">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="a2cbe-282">El elemento `CustomUserFactory` que se muestra en la sección [Grupos definidos por el usuario y roles de administrador de ADD](#user-defined-groups-and-administrator-roles) está configurado para actuar a partir de una notificación `roles` con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-282">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="a2cbe-283">Agregue y registre el elemento `CustomUserFactory` en la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada, como se muestra en la sección [Grupos definidos por el usuario y roles de administrador de AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-283">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="a2cbe-284">No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-284">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="a2cbe-285">En el archivo `Program.Main` de la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada, especifique la notificación denominada "`role`" como la notificación de rol:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-285">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="a2cbe-286">Los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-286">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a2cbe-287">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol `admin` para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-287">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="a2cbe-288">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-288">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a2cbe-289">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-289">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a2cbe-290">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="a2cbe-290">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a2cbe-291">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="a2cbe-291">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="a2cbe-292">Identificadores de objeto de rol de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-292">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="a2cbe-293">Los identificadores de objeto reunidos en la siguiente tabla sirven para crear [directivas](xref:security/authorization/policies) para notificaciones `group`.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-293">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="a2cbe-294">Las directivas permiten a una aplicación autorizar a usuarios para que realicen diversas actividades en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2cbe-294">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="a2cbe-295">Para más información, consulte la sección [Grupos definidos por el usuario y roles de administrador de AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="a2cbe-295">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="a2cbe-296">Rol de administrador de AAD</span><span class="sxs-lookup"><span data-stu-id="a2cbe-296">AAD Administrator Role</span></span> | <span data-ttu-id="a2cbe-297">Id. de objeto</span><span class="sxs-lookup"><span data-stu-id="a2cbe-297">Object ID</span></span>
--- | ---
<span data-ttu-id="a2cbe-298">Administrador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="a2cbe-298">Application administrator</span></span> | <span data-ttu-id="a2cbe-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="a2cbe-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="a2cbe-300">Desarrollador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="a2cbe-300">Application developer</span></span> | <span data-ttu-id="a2cbe-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="a2cbe-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="a2cbe-302">Administrador de autenticación</span><span class="sxs-lookup"><span data-stu-id="a2cbe-302">Authentication administrator</span></span> | <span data-ttu-id="a2cbe-303">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="a2cbe-303">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="a2cbe-304">Administrador de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a2cbe-304">Azure DevOps administrator</span></span> | <span data-ttu-id="a2cbe-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="a2cbe-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="a2cbe-306">Administrador de Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="a2cbe-306">Azure Information Protection administrator</span></span> | <span data-ttu-id="a2cbe-307">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="a2cbe-307">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="a2cbe-308">Administrador de conjuntos de claves B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="a2cbe-308">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="a2cbe-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="a2cbe-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="a2cbe-310">Administrador de directivas B2C con IEF</span><span class="sxs-lookup"><span data-stu-id="a2cbe-310">B2C IEF Policy administrator</span></span> | <span data-ttu-id="a2cbe-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="a2cbe-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="a2cbe-312">Administrador de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="a2cbe-312">B2C user flow administrator</span></span> | <span data-ttu-id="a2cbe-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="a2cbe-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="a2cbe-314">Administrador de atributos de flujos de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="a2cbe-314">B2C user flow attribute administrator</span></span> | <span data-ttu-id="a2cbe-315">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="a2cbe-315">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="a2cbe-316">Administrador de facturación</span><span class="sxs-lookup"><span data-stu-id="a2cbe-316">Billing administrator</span></span> | <span data-ttu-id="a2cbe-317">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="a2cbe-317">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="a2cbe-318">Administrador de aplicaciones en la nube</span><span class="sxs-lookup"><span data-stu-id="a2cbe-318">Cloud application administrator</span></span> | <span data-ttu-id="a2cbe-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="a2cbe-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="a2cbe-320">Administrador de dispositivos en la nube</span><span class="sxs-lookup"><span data-stu-id="a2cbe-320">Cloud device administrator</span></span> | <span data-ttu-id="a2cbe-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="a2cbe-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="a2cbe-322">Administrador de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="a2cbe-322">Compliance administrator</span></span> | <span data-ttu-id="a2cbe-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="a2cbe-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="a2cbe-324">Administrador de datos de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="a2cbe-324">Compliance data administrator</span></span> | <span data-ttu-id="a2cbe-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="a2cbe-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="a2cbe-326">Administrador de acceso condicional</span><span class="sxs-lookup"><span data-stu-id="a2cbe-326">Conditional Access administrator</span></span> | <span data-ttu-id="a2cbe-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="a2cbe-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="a2cbe-328">Aprobador del acceso a la Caja de seguridad del cliente</span><span class="sxs-lookup"><span data-stu-id="a2cbe-328">Customer LockBox access approver</span></span> | <span data-ttu-id="a2cbe-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="a2cbe-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="a2cbe-330">Administrador de Análisis de escritorio</span><span class="sxs-lookup"><span data-stu-id="a2cbe-330">Desktop Analytics administrator</span></span> | <span data-ttu-id="a2cbe-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="a2cbe-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="a2cbe-332">Lectores de directorios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-332">Directory readers</span></span> | <span data-ttu-id="a2cbe-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="a2cbe-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="a2cbe-334">Administrador de Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="a2cbe-334">Dynamics 365 administrator</span></span> | <span data-ttu-id="a2cbe-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="a2cbe-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="a2cbe-336">Administrador de Exchange</span><span class="sxs-lookup"><span data-stu-id="a2cbe-336">Exchange administrator</span></span> | <span data-ttu-id="a2cbe-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="a2cbe-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="a2cbe-338">Administrador de proveedor de Identity externo</span><span class="sxs-lookup"><span data-stu-id="a2cbe-338">External Identity Provider administrator</span></span> | <span data-ttu-id="a2cbe-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="a2cbe-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="a2cbe-340">Administrador global</span><span class="sxs-lookup"><span data-stu-id="a2cbe-340">Global administrator</span></span> | <span data-ttu-id="a2cbe-341">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="a2cbe-341">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="a2cbe-342">Lector global</span><span class="sxs-lookup"><span data-stu-id="a2cbe-342">Global reader</span></span> | <span data-ttu-id="a2cbe-343">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="a2cbe-343">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="a2cbe-344">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="a2cbe-344">Groups administrator</span></span> | <span data-ttu-id="a2cbe-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="a2cbe-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="a2cbe-346">Invitador de usuarios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-346">Guest inviter</span></span> | <span data-ttu-id="a2cbe-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="a2cbe-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="a2cbe-348">Administrador del departamento de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="a2cbe-348">Helpdesk administrator</span></span> | <span data-ttu-id="a2cbe-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="a2cbe-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="a2cbe-350">Administrador de Intune</span><span class="sxs-lookup"><span data-stu-id="a2cbe-350">Intune administrator</span></span> | <span data-ttu-id="a2cbe-351">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="a2cbe-351">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="a2cbe-352">Administrador de Kaizala</span><span class="sxs-lookup"><span data-stu-id="a2cbe-352">Kaizala administrator</span></span> | <span data-ttu-id="a2cbe-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="a2cbe-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="a2cbe-354">Administrador de licencias</span><span class="sxs-lookup"><span data-stu-id="a2cbe-354">License administrator</span></span> | <span data-ttu-id="a2cbe-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="a2cbe-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="a2cbe-356">Lector de privacidad del Centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="a2cbe-356">Message center privacy reader</span></span> | <span data-ttu-id="a2cbe-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="a2cbe-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="a2cbe-358">Lector del centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="a2cbe-358">Message center reader</span></span> | <span data-ttu-id="a2cbe-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="a2cbe-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="a2cbe-360">Administrador de aplicaciones de Office</span><span class="sxs-lookup"><span data-stu-id="a2cbe-360">Office apps administrator</span></span> | <span data-ttu-id="a2cbe-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="a2cbe-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="a2cbe-362">Administrador de contraseñas</span><span class="sxs-lookup"><span data-stu-id="a2cbe-362">Password administrator</span></span> | <span data-ttu-id="a2cbe-363">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="a2cbe-363">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="a2cbe-364">Administrador de Power BI</span><span class="sxs-lookup"><span data-stu-id="a2cbe-364">Power BI administrator</span></span> | <span data-ttu-id="a2cbe-365">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="a2cbe-365">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="a2cbe-366">Administrador de Power Platform</span><span class="sxs-lookup"><span data-stu-id="a2cbe-366">Power platform administrator</span></span> | <span data-ttu-id="a2cbe-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="a2cbe-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="a2cbe-368">Administrador de autenticación con privilegios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-368">Privileged authentication administrator</span></span> | <span data-ttu-id="a2cbe-369">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="a2cbe-369">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="a2cbe-370">Administrador de roles con privilegios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-370">Privileged role administrator</span></span> | <span data-ttu-id="a2cbe-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="a2cbe-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="a2cbe-372">Lector de informes</span><span class="sxs-lookup"><span data-stu-id="a2cbe-372">Reports reader</span></span> | <span data-ttu-id="a2cbe-373">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="a2cbe-373">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="a2cbe-374">Administrador de búsqueda</span><span class="sxs-lookup"><span data-stu-id="a2cbe-374">Search administrator</span></span> | <span data-ttu-id="a2cbe-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="a2cbe-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="a2cbe-376">Editor de búsqueda</span><span class="sxs-lookup"><span data-stu-id="a2cbe-376">Search editor</span></span> | <span data-ttu-id="a2cbe-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="a2cbe-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="a2cbe-378">Administrador de seguridad</span><span class="sxs-lookup"><span data-stu-id="a2cbe-378">Security administrator</span></span> | <span data-ttu-id="a2cbe-379">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="a2cbe-379">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="a2cbe-380">Operador de seguridad</span><span class="sxs-lookup"><span data-stu-id="a2cbe-380">Security operator</span></span> | <span data-ttu-id="a2cbe-381">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="a2cbe-381">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="a2cbe-382">Lector de seguridad</span><span class="sxs-lookup"><span data-stu-id="a2cbe-382">Security reader</span></span> | <span data-ttu-id="a2cbe-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="a2cbe-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="a2cbe-384">Administrador del soporte técnico del servicio</span><span class="sxs-lookup"><span data-stu-id="a2cbe-384">Service support administrator</span></span> | <span data-ttu-id="a2cbe-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="a2cbe-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="a2cbe-386">Administrador de SharePoint</span><span class="sxs-lookup"><span data-stu-id="a2cbe-386">SharePoint administrator</span></span> | <span data-ttu-id="a2cbe-387">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="a2cbe-387">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="a2cbe-388">Administrador de Skype Empresarial</span><span class="sxs-lookup"><span data-stu-id="a2cbe-388">Skype for Business administrator</span></span> | <span data-ttu-id="a2cbe-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="a2cbe-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="a2cbe-390">Administrador de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a2cbe-390">Teams Communications Administrator</span></span> | <span data-ttu-id="a2cbe-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="a2cbe-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="a2cbe-392">Ingeniero de soporte técnico de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a2cbe-392">Teams Communications Support Engineer</span></span> | <span data-ttu-id="a2cbe-393">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="a2cbe-393">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="a2cbe-394">Especialista de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="a2cbe-394">Teams Communications Specialist</span></span> | <span data-ttu-id="a2cbe-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="a2cbe-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="a2cbe-396">Administrador de servicios de Teams</span><span class="sxs-lookup"><span data-stu-id="a2cbe-396">Teams Service Administrator</span></span> | <span data-ttu-id="a2cbe-397">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="a2cbe-397">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="a2cbe-398">Administrador de usuarios</span><span class="sxs-lookup"><span data-stu-id="a2cbe-398">User administrator</span></span> | <span data-ttu-id="a2cbe-399">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="a2cbe-399">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2cbe-400">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a2cbe-400">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
