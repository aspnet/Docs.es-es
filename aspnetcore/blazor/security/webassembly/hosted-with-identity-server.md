---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación Blazor WebAssembly hospedada de ASP.NET Core con Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 6ae8c55fcfc85dc725a7dd20a7dbecba063a13e9
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900795"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas API.

> [!NOTE]
> Para configurar una aplicación Blazor WebAssembly independiente u hospedada para que use una instancia de Identity Server externa existente, siga las instrucciones de <xref:blazor/security/webassembly/standalone-with-authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:

1. Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.

1. Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.

1. Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core) en la sección **Avanzado**.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code y CLI de .NET Core](#tab/visual-studio-code+netcore-cli)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía, especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar los usuarios dentro de la aplicación con el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Marcador de posición  | Ejemplo        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:

1. En el paso **Configure your new Blazor WebAssembly App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).

1. La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [Identity](xref:security/authentication/identity).

1. Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core).

---

## <a name="server-app-configuration"></a>Configuración de la aplicación *`Server`*

En las siguientes secciones se describen algunas adiciones al proyecto cuando se incluye compatibilidad con la autenticación.

### <a name="startup-class"></a>Clase Startup

La clase `Startup` tiene las siguientes adiciones.

* En `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura convenciones de ASP.NET Core predeterminadas por encima de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* En `Startup.Configure`:

  * El middleware IdentityServer expone los puntos de conexión de OpenID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * El middleware Authentication es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware Authorization habilita capacidades de autorización:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) en escenarios de ASP.NET Core. IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones. IdentityServer expone las complejidades innecesarias en los escenarios más comunes. En consecuencia, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida. Cuando sus necesidades de autenticación cambien, tendrá a su disposición toda la eficacia de IdentityServer para personalizar la autenticación para adaptarse a los requisitos de una aplicación.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La directiva está configurada para permitir que Identity controle todas las solicitudes enrutadas a cualquier subruta en el espacio de dirección URL de Identity `/Identity`. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se encarga de todas las demás solicitudes. Este método también hace lo siguiente:

* Registra un recurso de API `{APPLICATION NAME}API` con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.
* Configura el middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase. Este atributo señala que, para acceder al recurso, el usuario debe estar autorizado en función de la directiva predeterminada. La directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura. El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado de las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer. La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obtener el control total del esquema de la base de datos, herede de una de las clases <xref:Microsoft.EntityFrameworkCore.DbContext> de Identity disponibles y configure el contexto para incluir el esquema de Identity llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), el punto de conexión del cliente se aprovisiona para proporcionar parámetros de OIDC.

### <a name="app-settings"></a>Configuración de la aplicación

En el archivo de configuración de la aplicación (`appsettings.json`), en la raíz del proyecto, la sección `IdentityServer` describe la lista de clientes configurados. En el siguiente ejemplo hay un solo cliente, cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth. El perfil señala el tipo de aplicación que se está configurando. El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.Client`).

## <a name="client-app-configuration"></a>Configuración de la aplicación *`Client`*

### <a name="authentication-package"></a>Paquete de autenticación

Cuando una aplicación se crea para usar cuentas de usuario individuales (`Individual`), dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>Configuración de `HttpClient`

En `Program.Main` (`Program.cs`), hay configurada una instancia de <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) con nombre para proporcionar instancias de <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Si va a configurar una aplicación Blazor WebAssembly para usar una instancia de Identity Server existente que no forma parte de una solución hospedada por Blazor, cambie el registro de la dirección base <xref:System.Net.Http.HttpClient> de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) a la dirección URL del punto de conexión de autorización de API de la aplicación de servidor.

### <a name="api-authorization-support"></a>Compatibilidad con autorización de API

La compatibilidad para autenticar usuarios se incluye en el contenedor de servicios con el método de extensión proporcionado dentro del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). Este método configura los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.

```csharp
builder.Services.AddApiAuthorization();
```

La configuración de la aplicación se carga por convención de forma predeterminada desde `_configuration/{client-id}`. Por convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación. Esta dirección URL se puede cambiar para que apunte a un punto de conexión aparte llamando a la sobrecarga con opciones.

### <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente App

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:

* En el caso de los usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Proporciona un vínculo a la página de perfil de usuario en ASP.NET Core Identity.
  * Proporciona un botón para cerrar la sesión de la aplicación.
* En el caso de los usuarios anónimos:
  * Ofrece la opción de registrarse.
  * Ofrece la opción de iniciar sesión.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede hacer lo siguiente:

* Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.
* Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.

## <a name="name-and-role-claim-with-api-authorization"></a>Notificaciones name y role con autorización de API

### <a name="custom-user-factory"></a>Fábrica de usuario personalizada

En la aplicación *`Client`* , cree una fábrica de usuario personalizada. Identity Server envía varios roles como una matriz JSON en una sola notificación `role`. Se envía un único rol como un valor de cadena en la notificación. La fábrica crea una notificación `role` individual por cada uno de los roles del usuario.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

En la aplicación *`Client`* , registre la fábrica en `Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

En la aplicación *`Server`* , llame a <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> en el generador de Identity, que agrega servicios relativos a roles:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Configuración de Identity Server

Siga **uno** de estos procedimientos:

* [Opciones de autorización de API](#api-authorization-options)
* [Servicio de perfil](#profile-service)

#### <a name="api-authorization-options"></a>Opciones de autorización de API

En la aplicación *`Server`* :

* Configure Identity Server para colocar las notificaciones `name` y `role` en el token de identificador y el token de acceso.
* Evite la asignación predeterminada de roles en el controlador de token JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Servicio de perfil

En la aplicación *`Server`* , cree una implementación `ProfileService`.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

En la aplicación *`Server`* , registre el servicio de perfil en `Startup.ConfigureServices`:

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Uso de mecanismos de autorización

En la aplicación *`Client`* , los métodos de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:

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

`User.Identity.Name` se rellena en la aplicación *`Client`* con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Host en Azure App Service con un dominio personalizado

En la siguiente guía se explica cómo implementar una aplicación hospedada de Blazor WebAssembly con Identity Server en [Azure App Service](https://azure.microsoft.com/services/app-service/) con un dominio personalizado.

En este escenario de hospedaje, **no** use el mismo certificado para la [clave de firma de tokens de Identity Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) y la comunicación segura HTTPS del sitio con exploradores:

* Usar certificados diferentes para estos dos requisitos es una buena práctica de seguridad porque aísla claves privadas para cada propósito.
* Los certificados TLS para la comunicación con los exploradores se administran de forma independiente sin afectar a la firma de tokens de Identity Server.
* Cuando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) proporciona un certificado a una aplicación de App Service para el enlace de dominio personalizado, Identity Server no puede obtener el mismo certificado de Azure Key Vault para la firma de tokens. Aunque es posible configurar Identity Server para que use el mismo certificado TLS desde una ruta de acceso física, la colocación de los certificados de seguridad en el control de código fuente es una **práctica inadecuada y debe evitarse en la mayoría de los casos**.

En la siguiente guía, se crea un certificado autofirmado en Azure Key Vault únicamente para la firma de tokens de Identity Server. La configuración de Identity Server usa el certificado del almacén de claves mediante el almacén de certificados `My` > `CurrentUser` de la aplicación. Otros certificados que se usan para el tráfico HTTPS con dominios personalizados se crean y se configuran de forma independiente del certificado de firma de Identity Server.

Para configurar una aplicación, Azure App Service y Azure Key Vault para que hospeden con un dominio personalizado y HTTPS:

1. Cree un [plan de App Service](/azure/app-service/overview-hosting-plans) con un nivel de plan de `Basic B1` o superior. App Service requiere un nivel de servicio `Basic B1` o superior para usar dominios personalizados.
1. Cree un certificado PFX para la comunicación segura del explorador del sitio (protocolo HTTPS) con un nombre común del nombre de dominio completo (FQDN) del sitio que controla la organización (por ejemplo, `www.contoso.com`). Cree el certificado con:
   * Usos de las claves
     * Validación de firma digital (`digitalSignature`)
     * Cifrado de claves (`keyEncipherment`)
   * Usos mejorados/extendidos de las claves
     * Autenticación de cliente (1.3.6.1.5.5.7.3.2)
     * Autenticación del servidor (1.3.6.1.5.5.7.3.1)

   Para crear el certificado, use uno de los métodos siguientes, o cualquier otra herramienta o servicio en línea adecuado:

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert en Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Anote la contraseña, que se usará más adelante para importar el certificado en Azure Key Vault.

   Para más información sobre los certificados de Azure Key Vault, consulte [Azure Key Vault: Certificados](/azure/key-vault/certificates/).
1. Cree una nueva instancia de Azure Key Vault o use un almacén de claves existente en su suscripción de Azure.
1. En el área **Certificados** del almacén de claves, importe el certificado del sitio PFX. Registre la huella digital del certificado, que se usará en la configuración de la aplicación más adelante.
1. En Azure Key Vault, genere un nuevo certificado autofirmado para la firma de tokens de Identity Server. Asigne al certificado un **Nombre de certificado** y un **Firmante**. El **Firmante** se especifica como `CN={COMMON NAME}`, donde el marcador de posición `{COMMON NAME}` es el nombre común del certificado. El nombre común puede ser cualquier cadena alfanumérica. Por ejemplo, `CN=IdentityServerSigning` es un **Firmante** válido de certificado. Use la configuración predeterminada de la **Configuración avanzada de directivas**. Registre la huella digital del certificado, que se usará en la configuración de la aplicación más adelante.
1. Vaya a Azure App Service en Azure Portal y cree un nuevo App Service con la siguiente configuración:
   * **Publicar** establecido en `Code`.
   * **Pila en tiempo de ejecución** establecido en el tiempo de ejecución de la aplicación.
   * Para **SKU y tamaño**, confirme que el nivel de App Service sea `Basic B1` o superior.  App Service requiere un nivel de servicio `Basic B1` o superior para usar dominios personalizados.
1. Después de que Azure cree App Service, abra la **Configuración** de la aplicación y agregue una nueva configuración de aplicación que especifique las huellas digitales del certificado que se registraron anteriormente. La clave de configuración de la aplicación es `WEBSITE_LOAD_CERTIFICATES`. Separe las huellas digitales de certificado en el valor de configuración de la aplicación con una coma, como se muestra en el ejemplo siguiente:
   * Clave: `WEBSITE_LOAD_CERTIFICATES`
   * Valor: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   En Azure Portal, guardar la configuración de la aplicación es un proceso de dos pasos: Guarde la configuración de clave-valor de `WEBSITE_LOAD_CERTIFICATES` y, después, seleccione el botón **Guardar**, en la parte superior de la hoja.
1. Seleccione la **configuración de TLS/SSL** de la aplicación. Seleccione **Certificados de clave privada (.pfx)** . Use el proceso **Importar certificado de Key Vault** dos veces para importar el certificado del sitio para la comunicación HTTPS y el certificado autofirmado del sitio para la firma de tokens de Identity Server.
1. Vaya a la hoja **Dominios personalizados**. En el sitio web del registrador de dominios, use la **dirección IP** y el **id. de verificación del dominio personalizado** para configurar el dominio. Una configuración de dominio típica incluye:
   * Un **registro A** con un **host** de `@` y un valor de la dirección IP de Azure Portal.
   * Un **registro TXT** con un **host** de `asuid` y el valor del id. de verificación generado por Azure y proporcionado por Azure Portal.

   Asegúrese de guardar correctamente los cambios en el sitio web del registrador de dominios. Algunos sitios web del registrador requieren un proceso de dos pasos para guardar los registros de dominio: Uno o varios registros se guardan individualmente seguidos de la actualización del registro del dominio con un botón independiente.
1. Vuelva a la hoja **Dominios personalizados** en Azure Portal. Seleccione **Agregar dominio personalizado**. Seleccione la opción **Registro A**. Proporcione el dominio y seleccione **Validar**. Si los registros de dominio son correctos y están propagados por Internet, el portal le permitirá seleccionar el botón **Agregar dominio personalizado**.

   Los cambios en el registro de dominio pueden tardar unos días en propagarse entre los servidores de nombres de dominio (DNS) de Internet después de que el registrador de dominios los procese. Si los registros de dominio no se actualizan en un plazo de tres días laborables, confirme que los registros se han establecido correctamente con el registrador de dominios y póngase en contacto con el servicio de soporte técnico al cliente.
1. En la hoja **Dominios personalizados**, el **ESTADO SSL** del dominio está marcado como `Not Secure`. Seleccione el vínculo **Agregar enlaces**. Seleccione el certificado HTTPS del sitio en el almacén de claves para el enlace de dominio personalizado.
1. En Visual Studio, abra el archivo de configuración de la aplicación del proyecto *Server* (`appsettings.json` o `appsettings.Production.json`). En la configuración de Identity Server, agregue la siguiente sección de `Key`. Especifique el **Firmante** del certificado autofirmado para la clave `Name`. En el ejemplo siguiente, el nombre común del certificado asignado en el almacén de claves es `IdentityServerSigning`, lo que produce un **Firmante** de `CN=IdentityServerSigning`:

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. En Visual Studio, cree un [perfil de publicación](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) de Azure App Service para el proyecto *Server*. En la barra de menús, seleccione: **Compilar** > **Publicar** > **Nuevo** > **Azure** > **Azure App Service** (Windows o Linux). Cuando Visual Studio se conecta a una suscripción de Azure, puede establecer la **Vista** de recursos de Azure por **Tipo de recurso**. Navegue dentro de la lista **Aplicación web** para buscar la instancia de App Service de la aplicación y selecciónela. Seleccione **Finalizar**.
1. Cuando Visual Studio vuelve a la ventana **Publicar**, se detectan automáticamente el almacén de claves y las dependencias del servicio de base de datos SQL Server.

   No se requiere ningún cambio de configuración en la configuración predeterminada para el servicio del almacén de claves.

   Con fines de prueba, la base de datos [SQLite](https://www.sqlite.org/index.html) local de una aplicación, que está configurada de forma predeterminada por la plantilla de Blazor, puede implementarse con la aplicación sin configuración adicional. La configuración de una base de datos diferente en producción para Identity Server está fuera del ámbito de este artículo. Para obtener más información, consulte los recursos de base de datos en los siguientes recursos:
   * [App Service](/azure/app-service/)
   * [Servidor de Identity](https://identityserver4.readthedocs.io/en/latest/)

1. Seleccione el enlace **Editar** debajo del nombre del perfil de implementación en la parte superior de la ventana. Cambie la dirección URL de destino a la dirección URL de dominio personalizada del sitio (por ejemplo, `https://www.contoso.com`). Guarde la configuración
1. Publique la aplicación. Visual Studio abre una ventana del explorador y solicita el sitio en su dominio personalizado.

La documentación de Azure contiene detalles adicionales sobre el uso de servicios de Azure y de dominios personalizados con enlace de TLS en App Service, incluida información sobre el uso de registros CNAME en lugar de registros A. Para obtener más información, vea los siguientes recursos:

* [Documentación de App Service](/azure/app-service/)
* [Tutorial: Asignación de un nombre DNS personalizado existente a Azure App Service](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Protección de un nombre DNS personalizado con un enlace TLS/SSL en Azure App Service](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Se recomienda usar una nueva ventana del explorador en privado o en incógnito para cada ejecución de prueba de la aplicación después de un cambio en la aplicación, la configuración de la aplicación o los servicios de Azure en Azure Portal. Los elementos cookie persistentes de una serie de pruebas anterior pueden dar errores de autenticación o autorización al probar el sitio, incluso aunque la configuración del sitio sea correcta. Para obtener más información sobre cómo configurar Visual Studio para abrir una nueva ventana de explorador en privado o incógnito para cada serie de pruebas, consulte la sección [Cookies y datos del sitio](#cookies-and-site-data).

Cuando se cambia la configuración de App Service en Azure Portal, las actualizaciones suelen surtir efecto rápidamente, pero no son instantáneas. A veces, debe esperar un breve período para que App Service se reinicie y se aplique un cambio de configuración.

Si va a solucionar un problema de carga de certificados, ejecute el siguiente comando en un shell de comandos [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) de PowerShell en Azure Portal. El comando proporciona una lista de certificados a los que la aplicación puede tener acceso desde el almacén de certificados de `My` > `CurrentUser`. La salida incluye firmantes de certificados y huellas digitales útiles al depurar una aplicación:

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Implementación en Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importación de un certificado de Key Vault (documentación de Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
