---
title: Blazor WebAssembly en ASP.NET Core con grupos y roles de Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly para usar grupos y roles de Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 68071be9fb9f7a097c0c3693293bf8295e0173f1
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818812"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Grupos de Azure AD, roles administrativos y roles definidos por el usuario

Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) proporciona diversos métodos de autorización que se pueden combinar con Identity de ASP.NET Core:

* Grupos definidos por el usuario
  * Seguridad
  * O365
  * Distribución
* Roles
  * Roles administrativos integrados
  * Roles definidos por el usuario

Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:

* [Independiente con cuentas Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Independiente con AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hospedado con AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Permisos para Microsoft Graph API

Se requiere una llamada a [Microsoft Graph API](/graph/use-the-api) para cualquier usuario de la aplicación con más de cinco pertenencias a grupos de seguridad y roles de administrador de AAD integrados.

Para permitir llamadas a Graph API, proporcione a la aplicación cliente o independiente de una solución de Blazor hospedada cualquiera de los siguientes [permisos de Graph API](/graph/permissions-reference) en Azure Portal:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` es el permiso con privilegios mínimos y es el que se usa para el ejemplo que se describe en este artículo.

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>Grupos definidos por el usuario y roles administrativos integrados

Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia de tipo `groups`, vea los siguientes artículos sobre Azure. Asigne usuarios a grupos de AAD definidos por el usuario y a roles administrativos integrados.

* [Roles que usan grupos de seguridad de Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [Atributo `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

En los siguientes ejemplos se da por hecho que un usuario está asignado al rol integrado de AAD *Administrador de facturación*.

La notificación `groups` única enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON. La aplicación debe convertir esta matriz JSON de grupos y roles en notificaciones `group` individuales, a partir de las cuales la aplicación podrá crear [directivas](xref:security/authorization/policies).

Cuando el número de roles administrativos y grupos definidos por el usuario de Azure integrados es superior a cinco, AAD envía una notificación `hasgroups` con un valor `true` en lugar de enviar una notificación `groups`. Cualquier aplicación que pueda tener más de cinco roles y grupos asignados a sus usuarios debe realizar una llamada a Graph API independiente para obtener los roles y grupos de un usuario. En la implementación de ejemplo que se proporciona en este artículo se aborda este escenario. Para más información, vea la información sobre las notificaciones `groups` y `hasgroups` en el artículo [Tokens de acceso de la Plataforma de identidad de Microsoft: Notificaciones de carga](/azure/active-directory/develop/access-tokens#payload-claims).

Extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz de grupos y roles. Asigne una matriz vacía a cada propiedad para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach` más adelante.

`CustomUserAccount.cs`:

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

En la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, cree una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada. Utilice el ámbito correcto (permiso) para las llamadas a Graph API que obtienen información de roles y grupos.

`GraphAPIAuthorizationMessageHandler.cs`:

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

En `Program.Main` (`Program.cs`), agregue el servicio de implementación <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> y agregue un elemento con nombre <xref:System.Net.Http.HttpClient> para realizar solicitudes a Graph API. En el siguiente ejemplo se nombra el cliente `GraphAPI`:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Cree clases de objetos de directorio de AAD para recibir los roles y grupos de Open Data Protocol (OData) desde una llamada a Graph API. OData se presenta en formato JSON, y una llamada a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> rellena una instancia de la clase `DirectoryObjects`.

`DirectoryObjects.cs`:

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

Cree una fábrica de usuario personalizada para procesar notificaciones de roles y grupos. En la implementación de ejemplo siguiente también se controla la matriz de notificaciones `roles`, que se describe en la sección [Roles definidos por el usuario](#user-defined-roles). Si la notificación `hasgroups` está presente, el elemento <xref:System.Net.Http.HttpClient> con nombre se usa para hacer una solicitud autorizada a Graph API para obtener los roles y grupos del usuario. Esta implementación usa el punto de conexión de la plataforma de Identity de Microsoft v1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentación de API](/graph/api/user-list-memberof)). Las instrucciones de este tema se actualizarán para Identity v2.0 cuando se actualicen los paquetes MSAL para v2.0.

`CustomAccountFactory.cs`:

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

No es necesario incluir código para quitar la notificación `groups` original, si está presente, ya que el marco de trabajo la quitará automáticamente.

> [!NOTE]
> El enfoque en este ejemplo:
>
> * Agrega una clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> personalizada para adjuntar tokens de acceso a solicitudes salientes.
> * Agrega un elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes de API web a un punto de conexión de API web externo seguro.
> * Utiliza el elemento <xref:System.Net.Http.HttpClient> con nombre para hacer solicitudes autorizadas.
>
> La cobertura general de este enfoque se encuentra en el artículo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.

Registre la fábrica en el archivo `Program.Main` (`Program.cs`) de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada. Dé el consentimiento en el ámbito de permiso de `Directory.Read.All` como un ámbito adicional para la aplicación:

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

Cree una [directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`. En el siguiente ejemplo se crea una directiva para el rol integrado *Administrador de facturación* de AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Para obtener una lista completa de los identificadores de objeto de rol de AAD, vea la sección [Identificadores de grupos de roles de AAD](#aad-adminstrative-role-group-ids).

En los siguientes ejemplos, la aplicación usa la directiva anterior para autorizar al usuario.

El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:

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

El acceso a un componente completo se puede basar en la directiva usando la [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.

También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic):

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

## <a name="user-defined-roles"></a>Roles definidos por el usuario

Una aplicación registrada en AAD también se puede configurar para usar roles definidos por el usuario.

Para configurar la aplicación en Azure Portal para proporcionar una notificación de pertenencia `roles`, vea [Procedimiento para agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.

En el siguiente ejemplo se da por hecho que una aplicación está configurada con dos roles:

* `admin`
* `developer`

> [!NOTE]
> Aunque no se pueden asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, sí se pueden asignar usuarios a roles y recibir una notificación `roles` relativa a los usuarios con una cuenta de Azure estándar. Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.
>
> Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.

La notificación `roles` única enviada por AAD presenta los roles definidos por el usuario como valores `value` de `appRoles` en una matriz JSON. La aplicación debe convertir esta matriz JSON de roles en notificaciones `role` individuales.

Los elementos `CustomUserFactory` que se muestran en la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles) están configurados para actuar a partir de una notificación `roles` con un valor de matriz JSON. Agregue y registre el elemento `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, tal y como se explica en la sección [Grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles). No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.

En el archivo `Program.Main` de la aplicación independiente o la aplicación cliente de una solución de Blazor hospedada, especifique la notificación denominada "`role`" como la notificación de rol:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Los métodos de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes puede usar un rol `admin` para autorizar al usuario:

* [Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)
* [Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)
* [Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)

  Se admiten pruebas de rol múltiple:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Identificadores de grupos de roles administrativos de AAD

Los identificadores de objeto reunidos en la siguiente tabla sirven para crear [directivas](xref:security/authorization/policies) para notificaciones `group`. Las directivas permiten a una aplicación autorizar a usuarios para que realicen diversas actividades en una aplicación. Para más información, vea la sección [Grupos definidos por el usuario y roles administrativos integrados](#user-defined-groups-and-built-in-administrative-roles).

Rol administrativo de AAD | Id. de objeto
--- | ---
Administrador de aplicaciones | fa11557b-4f15-4ddd-85d5-313c7cd74047
Desarrollador de aplicaciones | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrador de autenticación | 02d110a1-96b1-419e-af87-746461b60ed7
Administrador de Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrador de Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrador de conjuntos de claves B2C con IEF | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrador de directivas B2C con IEF | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrador de flujos de usuario B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrador de atributos de flujos de usuario B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrador de facturación | 69ff516a-b57d-4697-a429-9de4af7b5609
Administrador de aplicaciones en la nube | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrador de dispositivos en la nube | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrador de cumplimiento | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrador de datos de cumplimiento | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrador de acceso condicional | 8f71a611-137d-49af-87ad-e97f1fd5da76
Aprobador del acceso a la Caja de seguridad del cliente | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrador de Análisis de escritorio | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Lectores de directorios | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrador de Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrador de Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Administrador de proveedor de Identity externo | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrador global | a45ba61b-44db-462c-924b-3b2719152588
Lector global | f6903b21-6aba-4124-b44c-76671796b9d5
Administrador de grupos | 158b3e5a-d89d-460b-92b5-3b34985f0197
Invitador de usuarios | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrador del departamento de soporte técnico | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrador de Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrador de Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrador de licencias | 3355458a-e423-44bf-8b98-4ac5e572cea5
Lector de privacidad del Centro de mensajes | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Lector del centro de mensajes | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrador de aplicaciones de Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrador de contraseñas | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrador de Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrador de Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrador de autenticación con privilegios | 0829f731-b46d-419f-9742-aeb122367d11
Administrador de roles con privilegios | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lector de informes | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrador de búsqueda | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor de búsqueda | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrador de seguridad | 20fa50e3-6531-44d8-bd39-b251420568ad
Operador de seguridad | 43aae017-8e51-4188-91ab-e6debd572800
Lector de seguridad | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrador del soporte técnico del servicio | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrador de SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrador de Skype Empresarial | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrador de comunicaciones de Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Ingeniero de soporte técnico de comunicaciones de Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Especialista de comunicaciones de Teams | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrador de servicios de Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrador de usuarios | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
