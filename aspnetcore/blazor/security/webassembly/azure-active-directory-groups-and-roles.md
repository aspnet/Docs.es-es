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
ms.openlocfilehash: c180580ec56313e444f2daf2b7d08c4d909b498a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280530"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Grupos, roles de administrador y roles de aplicación de Azure Active Directory (AAD)

Azure Active Directory (AAD) proporciona diversos enfoques de autorización que se pueden combinar con ASP.NET Core Identity:

* Grupos
  * Seguridad
  * Microsoft 365
  * Distribución
* Roles
  * Roles de administrador de AAD
  * Roles de aplicación

Las instrucciones de este artículo atañen a los escenarios de implementación de AAD de Blazor WebAssembly descritos en los siguientes temas:

* [Independiente con cuentas Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Independiente con AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hospedado con AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

En la guía del artículo se proporcionan instrucciones para las aplicaciones de cliente y servidor:

* **CLIENTE**: Aplicaciones Blazor WebAssembly independientes o la aplicación *`Client`* de una solución Blazor hospedada.
* **SERVIDOR**: Aplicaciones de API web/API de servidor de ASP.NET Core independientes o la aplicación *`Server`* de una solución Blazor hospedada.

## <a name="scopes"></a>Ámbitos

Para permitir llamadas de [Microsoft Graph API](/graph/use-the-api) para los datos de perfil de usuario, asignación de roles y pertenencia a grupos, el **CLIENTE** se configura con (`https://graph.microsoft.com/User.Read`) [el permiso Graph API (ámbito)](/graph/permissions-reference) en Azure Portal.

A una aplicación de **SERVIDOR** que llama a Graph API para obtener datos de pertenencia a roles y grupos se le proporciona `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [permiso de Graph API (ámbito)](/graph/permissions-reference) en Azure Portal.

Estos ámbitos son necesarios además de los ámbitos requeridos en los escenarios de implementación de AAD descritos en los temas que se enumeran en la primera sección de este artículo.

> [!NOTE]
> Las palabras "permiso" y "ámbito" se usan indistintamente en el Azure Portal y en varios juegos de documentación tanto de Microsoft como externos. En este artículo se utiliza la palabra "ámbito" para hacer referencia a los permisos asignados a una aplicación en Azure Portal.

## <a name="group-membership-claims-attribute"></a>Atributo de notificaciones de pertenencia a grupos

En el manifiesto de la aplicación de Azure Portal para las aplicaciones **CLIENTE** y **SERVIDOR**, establezca el [atributo `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) en `All`. Un valor de `All` se traduce en la obtención de todos los grupos de seguridad, grupos de distribución y roles de los que es miembro el usuario que inició sesión.

1. Abra el registro de Azure Portal de la aplicación.
1. Seleccione **Administrar** > **Manifiesto** en la barra lateral.
1. Busque el atributo `groupMembershipClaims`.
1. Establezca el valor en `All`.
1. Seleccione el botón **Guardar**.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Cuenta de usuario personalizada

Asigne usuarios a grupos de seguridad de AAD y roles de administrador de AAD en Azure Portal.

En los ejemplos de este artículo:

* Se supone que un usuario está asignado al rol de *administrador de facturación de AAD* en el inquilino de AAD de Azure Portal para que tenga autorización para acceder a los datos de la API del servidor.
* Se usan las [directivas de autorización](xref:security/authorization/policies) para controlar el acceso dentro de las aplicaciones **CLIENTE** y **SERVIDOR**.

En la aplicación **CLIENTE**, extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de:

* `Roles`; Matriz de roles de aplicación de AAD (se trata en la sección [Roles de aplicación](#app-roles))
* `Wids`; Roles de administrador de AAD en [notificaciones de identificadores conocidos (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`; [Notificación de identificador de objeto (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) inmutable (identifica de forma única a un usuario dentro de los inquilinos y entre ellos)

Asigne una matriz vacía a cada propiedad de matriz para que la comprobación de `null` no sea necesaria cuando se usen estas propiedades en los bucles `foreach`.

`CustomUserAccount.cs`:

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

Agregue una referencia de paquete al archivo de proyecto de la aplicación **CLIENTE** para [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).

Agregue la configuración y las clases de utilidad del SDK de Graph de la sección *SDK de Graph* del artículo <xref:blazor/security/webassembly/graph-api#graph-sdk>. En la clase `GraphClientExtensions`, especifique el ámbito de `User.Read` para el token de acceso en el método `AuthenticateRequestAsync`:

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Agregue la siguiente fábrica de cuentas de usuario personalizada a la aplicación **CLIENTE**. La fábrica de usuarios personalizada se usa para establecer:

* Notificaciones de rol de aplicación (`appRole`) (se trata en la sección [Roles de aplicación](#app-roles)).
* Notificaciones de rol de administrador de AAD (`directoryRole`).
* Un ejemplo de notificación de datos de perfil de usuario para el número de teléfono móvil del usuario (`mobilePhone`).
* Notificaciones de grupo de AAD (`directoryGroup`).

`CustomAccountFactory.cs`:

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

El código anterior no incluye pertenencias transitivas. Si la aplicación requiere notificaciones directas y transitivas de pertenencia a grupos, reemplace la propiedad `MemberOf` (`IUserMemberOfCollectionWithReferencesRequestBuilder`) por `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).

En el código anterior se omiten las notificaciones de pertenencia a grupos (`groups`) que son roles de administrador de AAD (tipo `#microsoft.graph.directoryRole`) porque los valores de GUID devueltos por Microsoft Identity Platform 2.0 son **identificadores de entidad** del rol de administrador de AAD y no [**identificadores de plantilla de rol**](/azure/active-directory/roles/permissions-reference#role-template-ids). Los identificadores de entidad no son estables en los inquilinos de Microsoft Identity Platform 2.0 y no deben usarse para crear directivas de autorización para los usuarios de las aplicaciones. Use siempre **identificadores de plantilla de rol** para roles de administrador de AAD **proporcionados por notificaciones `wids`** .

En `Program.Main` de la aplicación **CLIENTE**, configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada.

`Program.cs`:

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

## <a name="authorization-configuration"></a>Configuración de autorización

En la aplicación **CLIENTE**, cree una [directiva](xref:security/authorization/policies) para cada [rol de aplicación](#app-roles), rol de administrador de ADD o grupo de seguridad en `Program.Main`. En el siguiente ejemplo se crea una directiva para el rol *Administrador de facturación* de AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Para obtener una lista completa de los identificadores de los roles de administrador de AAD, consulte [Identificadores de plantilla de rol](/azure/active-directory/roles/permissions-reference#role-template-ids) en la documentación de Azure. Para obtener más información sobre las directivas de autorización, vea <xref:security/authorization/policies>.

En los siguientes ejemplos, la aplicación **CLIENTE** usa la directiva anterior para autorizar al usuario.

El [componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) funciona con la directiva:

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

El acceso a un componente completo se puede basar en la directiva usando una [directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.

También se puede [realizar una comprobación de directiva en el código con lógica de procedimientos](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

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

## <a name="authorize-server-apiweb-api-access"></a>Autorización del acceso a la API web/API de servidor

Una aplicación de API de **SERVIDOR** puede autorizar a los usuarios para que accedan a puntos de conexión de API seguros con [directivas de autorización](xref:security/authorization/policies) para grupos de seguridad, roles de administrador de AAD y roles de aplicación cuando un token de acceso contiene notificaciones `groups`, `wids`y `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`. En el ejemplo siguiente se crea una directiva para el rol de *administrador de facturación* de AAD en `Startup.ConfigureServices` con las notificaciones de `wids` (identificadores conocidos y de plantilla de rol):

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Para obtener una lista completa de los identificadores de los roles de administrador de AAD, consulte [Identificadores de plantilla de rol](/azure/active-directory/roles/permissions-reference#role-template-ids) en la documentación de Azure. Para obtener más información sobre las directivas de autorización, vea <xref:security/authorization/policies>.

El acceso a un controlador de la aplicación  **SERVIDOR** puede basarse en el uso de un [atributo `[Authorize]`](xref:security/authorization/simple) con el nombre de la directiva (documentación de la API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).

En el siguiente ejemplo se limita el acceso a los datos de facturación del elemento `BillingDataController` a los administradores de facturación de Azure con un nombre de directiva de `BillingAdministrator`:

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

Para obtener más información, vea <xref:security/authorization/policies>.

## <a name="app-roles"></a>Roles de aplicación

Para configurar la aplicación en Azure Portal para proporcionar notificaciones de pertenencia de roles de aplicación, consulte [Procedimiento: agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.

En el ejemplo siguiente se da por supuesto que las aplicaciones **CLIENTE** y **SERVIDOR** están configuradas con dos roles y los roles están asignados a un usuario de prueba:

* `admin`
* `developer`

> [!NOTE]
> Al desarrollar una aplicación Blazor WebAssembly hospedada o un par cliente-servidor de aplicaciones independientes (una aplicación Blazor WebAssembly independiente y una aplicación de API web/API de servidor de ASP.NET Core independiente), la propiedad del manifiesto `appRoles` de los registros de aplicaciones de Azure Portal de cliente y servidor deben incluir los mismos roles configurados. Después de establecer los roles en el manifiesto de la aplicación del cliente, cópielos en su totalidad en el manifiesto de la aplicación de servidor. Si no refleja el manifiesto `appRoles` entre los registros de aplicaciones de cliente y de servidor, las notificaciones de rol no se establecen para los usuarios autenticados de la API web o la API de servidor, aunque su token de acceso tenga las notificaciones de roles correctas.

> [!NOTE]
> Aunque no se pueden asignar roles a grupos sin una cuenta de Azure AD Premium, sí se pueden asignar roles a usuarios y recibir una notificación de roles relativa a los usuarios con una cuenta de Azure estándar. Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.
>
> Puede asignar varios roles en Azure Portal **_volviendo a agregar un usuario_** en cada asignación de rol adicional.

El valor `CustomAccountFactory` que se muestra en la sección [Cuenta de usuario personalizada](#custom-user-account) se configura para que actúe en una notificación de `roles` con un valor de matriz JSON. Agregue y registre `CustomAccountFactory` en la aplicación **CLIENTE** como se muestra en la sección [Cuenta de usuario personalizada](#custom-user-account). No es necesario incluir código para quitar la notificación `roles` original, ya que el marco la quitará automáticamente.

En `Program.Main` de una aplicación **CLIENTE**, especifique la notificación denominada "`appRole`" como la notificación de rol para las comprobaciones <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Si prefiere usar la notificación `directoryRoles` (roles de administrador de ADD), asigne "`directoryRoles`" a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.

En `Startup.ConfigureServices` de una aplicación **SERVIDOR**, especifique la notificación denominada "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" como la notificación de rol para las comprobaciones <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:

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
> Si prefiere usar la notificación `wids` (roles de administrador de ADD), asigne "`wids`" a <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.

Los métodos de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes de la aplicación **CLIENTE** puede usar el rol `admin` para autorizar al usuario:

* [Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Lógica de procedimientos](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Se admiten pruebas de rol múltiple:

* Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el componente `AuthorizeView`:

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el componente `AuthorizeView`:

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el atributo `[Authorize]`:

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el atributo `[Authorize]`:

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el código de procedimientos:

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

* Se requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el código de procedimientos cambiando el [OR condicional (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) a un [AND condicional (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) en el ejemplo anterior:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

Cualquiera de los mecanismos de autorización de los controladores de la aplicación **SERVIDOR** puede usar el rol `admin` para autorizar al usuario:

* [Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Lógica de procedimientos](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Se admiten pruebas de rol múltiple:

* Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el atributo `[Authorize]`:

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el atributo `[Authorize]`:

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Requiere que el usuario esté en **cualquiera** de los roles `admin` **o** `developer` con el código de procedimientos:

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

* Se requiere que el usuario esté en **ambos** roles `admin` **y** `developer` con el código de procedimientos cambiando el [OR condicional (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) a un [AND condicional (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) en el ejemplo anterior:

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Recursos adicionales

* [Identificadores de plantilla de rol (documentación de Azure)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [Atributo `groupMembershipClaims` (documentación de Azure)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Cómo: Incorporación de roles de aplicación a una aplicación y su recepción en el token (documentación de Azure)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Roles de aplicación (documentación de Azure)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
