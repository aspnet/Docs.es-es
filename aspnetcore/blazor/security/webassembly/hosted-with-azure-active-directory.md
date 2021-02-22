---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e38838930dba70abfcfe4db9c204132e67866041
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280943"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory

En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> En las aplicaciones Blazor WebAssembly creadas en Visual Studio que se han configurado para admitir cuentas de un directorio organizativo de AAD, Visual Studio no configura actualmente los proyectos de la solución o los registros de aplicaciones de Azure Portal correctamente durante la generación del proyecto. Esto se va a corregir en una próxima versión de Visual Studio.
>
> En este artículo se muestra cómo crear la solución y los registros del portal de aplicaciones de Azure con el comando de la CLI de .NET `dotnet new` y mediante la creación manual de los registros de aplicaciones en Azure Portal.
>
> Si prefiere crear la solución y los registros de aplicaciones de Azure con Visual Studio antes de que se actualice el IDE, consulte **_cada sección de este artículo_** y confirme o actualice las configuraciones de las aplicaciones y los registros de aplicaciones después de que Visual Studio cree la solución.

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a>Registro de aplicaciones en AAD y creación de una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones que encontrará en [Inicio rápido: Configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.

### <a name="register-a-server-api-app"></a>Registro de una aplicación de API de servidor

Registre una aplicación de AAD para la *aplicación de API de servidor*:

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD**).
1. Elija un **tipo de cuenta compatible**. En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).
1. La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.
1. Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.

Registre la siguiente información:

* Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.
* Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.
* Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.

En **Permisos de API**, quite el permiso **Microsoft Graph** > **User.Read**, ya que la aplicación no requiere inicio de sesión ni acceso de perfil de usuario.

En **Exponer una API**:

1. Seleccione **Agregar un ámbito**.
1. Seleccione **Guardar y continuar**.
1. Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).
1. Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).
1. Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).
1. Confirme que **Estado** está establecido en **Habilitado**.
1. Seleccione la opción **Agregar un ámbito**.

Registre la siguiente información:

* URI del identificador de aplicación (por ejemplo, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)
* Nombre del ámbito (por ejemplo, `API.Access`)

### <a name="register-a-client-app"></a>Registrar una aplicación cliente

Registre una aplicación de AAD para la *aplicación cliente*:

::: moniker range=">= aspnetcore-5.0"

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD**).
1. Elija un **tipo de cuenta compatible**. En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).
1. Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.

Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.

En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD**).
1. Elija un **tipo de cuenta compatible**. En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).
1. Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.

Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.

En **Autenticación** > **Configuraciones de plataforma** > **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

En **Permisos de API**:

1. Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read**.
1. Seleccione **Agregar un permiso**, seguido de **Mis API**.
1. Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD**).
1. Abra la lista **API**.
1. Habilite el acceso a la API (por ejemplo, `API.Access`).
1. Seleccione **Agregar permisos**.
1. Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** . Seleccione **Sí** para confirmar la acción.

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Marcador de posición                  | Nombre de Azure Portal                                     | Ejemplo                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | Identificador de aplicación (cliente) para la aplicación *`Client`*        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | Nombre de ámbito                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | Identificador de aplicación (cliente) para la *aplicación de API de servidor*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | URI de Id. de aplicación&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | Dominio Principal/Publicador/Inquilino                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | Id. de directorio (inquilino)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

&dagger;La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`. Al proporcionar el URI de identificador de aplicación para el marcador de posición `{SERVER API APP ID URI}` y si el esquema es `api://`, quite el esquema (`api://`) del argumento, como se muestra en el valor de ejemplo de la tabla anterior. Si el URI de identificador de aplicación es un valor personalizado o tiene algún otro esquema (por ejemplo, `https://` para un dominio de publicador que no es de confianza similar a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), debe actualizar manualmente el URI del ámbito predeterminado y quitar el esquema `api://` después de que la plantilla cree la aplicación *`Client`* . Para obtener más información, consulte la nota de la sección [Ámbitos de token de acceso](#access-token-scopes). La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios. Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Configuración de la aplicación](#app-settings).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Ámbitos de token de acceso](#access-token-scopes).

::: moniker-end

> [!NOTE]
> En Azure Portal, el valor de configuración de plataforma de la aplicación *`Client`* **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.
>
> Si la aplicación *`Client`* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.
>
> Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación *`Client`* , vuelva al registro de la aplicación *`Client`* en Azure Portal y actualice el URI de redirección con el puerto correcto.

## <a name="server-app-configuration"></a>Configuración de la aplicación *`Server`*

*Esta sección atañe a la aplicación **`Server`** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

::: moniker range=">= aspnetcore-5.0"

La compatibilidad con la autenticación y autorización de llamadas realizadas a las API web de ASP.NET Core con Microsoft Identity Platform se proporciona mediante los paquetes siguientes:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI):

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

::: moniker range=">= aspnetcore-5.0"

El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado. El método <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> configura servicios para proteger la API web con Microsoft Identity Platform v2.0. Este método espera una sección `AzureAd` en la configuración de la aplicación con los ajustes necesarios para inicializar las opciones de autenticación.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado. El método <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:

* La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.
* Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

De manera predeterminada, la API de la aplicación *`Server`* rellena `User.Identity.Name` con el valor del tipo de notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`:

* Agregue un espacio de nombres de <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> a `Startup.cs`:

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a>Configuración de la aplicación

::: moniker range=">= aspnetcore-5.0"

El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador. Es **importante** comprender esto:

* El [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.
* El [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuración de la aplicación *`Client`*

*Esta sección atañe a la aplicación **`Client`** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a la aplicación de forma transitiva.

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).

La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal). Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.

La configuración se suministra a través del archivo `wwwroot/appsettings.json`:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Ámbitos de token de acceso

Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:

* Están incluidos de forma predeterminada en la solicitud de inicio de sesión.
* Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.

Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory. En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`. Al generar una aplicación a partir de la plantilla de proyecto Blazor, confirme que el valor del ámbito de token de acceso predeterminado usa el valor de URI de identificador de aplicación personalizado correcto que proporcionó en Azure Portal o un valor con **uno** de los siguientes formatos:
>
> * Cuando el dominio del publicador del directorio es **de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspeccione el valor de un esquema doble (`api://api://...`). Si hay un esquema doble, quite el primer esquema `api://` del valor.
>
> * Cuando el dominio del publicador del directorio **no es de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspeccione el valor para ver si hay un esquema `api://` adicional (`api://https://contoso.onmicrosoft.com/...`). Si hay un esquema `api://` adicional, quite el esquema `api://` del valor.
>
> La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios. Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Especifique ámbitos adicionales con `AdditionalScopesToConsent`:

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

Para más información, vea las siguientes secciones del artículo *Otros escenarios*:

* [Solicitar tokens de acceso adicionales](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Adjuntar tokens a solicitudes salientes](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Modo de inicio de sesión

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a>Componente App

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede hacer lo siguiente:

* Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.
* Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/webassembly/additional-scenarios>
* [Creación de una versión personalizada de la biblioteca de JavaScript Authentication.MSAL](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
* [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app)
