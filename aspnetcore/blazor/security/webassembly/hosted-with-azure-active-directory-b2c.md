---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8727fa52acbcf59549c326bd5106e5dfe23c36be
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174321"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registro de aplicaciones en AAD B2C y creación de una solución

### <a name="create-a-tenant"></a>Creación de un inquilino

Siga las instrucciones que encontrará en [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C.

Registre la instancia de AAD B2C (por ejemplo, `https://contoso.b2clogin.com/`, barra diagonal final incluida). La instancia es el esquema y el host de un registro de la aplicación de Azure B2C, que se puede encontrar abriendo la ventana **Puntos de conexión** de la página **Registros de aplicaciones** de Azure Portal.

### <a name="register-a-server-api-app"></a>Registro de una aplicación de API de servidor

Siga las instrucciones que encontrará en [Tutorial: Registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* y, después, haga lo siguiente:

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD B2C**).
1. En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**
1. La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.
1. Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.
1. Seleccione **Registrar**.

Registre la siguiente información:

* Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.
* Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.

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

Siga de nuevo las instrucciones que encontrará en [Tutorial: Registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la aplicación *`Client`* y, después, haga lo siguiente:

::: moniker range=">= aspnetcore-5.0"

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD B2C**).
1. En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**
1. Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.
1. Seleccione **Registrar**.

1. Registre el identificador de aplicación (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.

En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.
1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD B2C**).
1. En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**
1. Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.
1. Seleccione **Registrar**.

Registre el identificador de aplicación (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.

En **Autenticación** > **Configuraciones de plataforma** > **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

En **Permisos de API**:

1. Seleccione **Agregar un permiso**, seguido de **Mis API**.
1. Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD B2C**).
1. Abra la lista **API**.
1. Habilite el acceso a la API (por ejemplo, `API.Access`).
1. Seleccione **Agregar permisos**.
1. Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** . Seleccione **Sí** para confirmar la acción.

En **Inicio** > **Azure AD B2C** > **Flujos de usuario**:

[Creación de un flujo de usuario de registro e inicio de sesión](/azure/active-directory-b2c/tutorial-create-user-flows)

Como mínimo, seleccione el atributo de usuario **Notificaciones de la aplicación** > **Nombre para mostrar** para rellenar `context.User.Identity.Name` en el componente `LoginDisplay` (`Shared/LoginDisplay.razor`).

Registre el nombre de flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Creación de la aplicación

Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Marcador de posición                   | Nombre de Azure Portal                                     | Ejemplo                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instancia                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | Identificador de aplicación (cliente) para la aplicación *`Client`*        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | Nombre de ámbito                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | Identificador de aplicación (cliente) para la *aplicación de API de servidor*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | URI de Id. de aplicación&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | Flujo de usuario de registro o de inicio de sesión                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | Dominio Principal/Publicador/Inquilino                       | `contoso.onmicrosoft.com`                      |

&dagger;La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`. Al proporcionar el URI de identificador de aplicación para el marcador de posición `{SERVER API APP ID URI}` y si el esquema es `api://`, quite el esquema (`api://`) del argumento, como se muestra en el valor de ejemplo de la tabla anterior. Si el URI de identificador de aplicación es un valor personalizado o tiene algún otro esquema (por ejemplo, `https://` para un dominio de publicador que no es de confianza similar a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), debe actualizar manualmente el URI del ámbito predeterminado y quitar el esquema `api://` después de que la plantilla cree la aplicación *`Client`* . Para obtener más información, consulte la nota de la sección [Ámbitos de token de acceso](#access-token-scopes). La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios. Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

> [!NOTE]
> El ámbito configurado por la plantilla de Blazor hospedada podría tener repetido el host de URI de identificador de aplicación. Confirme que el ámbito configurado para la colección `DefaultAccessTokenScopes` es correcto en `Program.Main` (`Program.cs`) de la aplicación *`Client`* .

> [!NOTE]
> En Azure Portal, el valor de configuración de plataforma de la aplicación *`Client`* **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.
>
> Si la aplicación *`Client`* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.
>
> Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación *`Client`* , vuelva al registro de la aplicación *`Client`* en Azure Portal y actualice el URI de redirección con el puerto correcto.

## <a name="server-app-configuration"></a>Configuración de la aplicación *`Server`*

*Esta sección atañe a la aplicación **`Server`** de la solución.*

### <a name="authentication-package"></a>Paquete de autenticación

La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI):

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

### <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado. El método <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:

* La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.
* Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>User.Identity.Name

`User.Identity.Name` no se rellena de forma predeterminada.

Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`, establezca <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configuración de la aplicación

El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Ejemplo:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador. Es **importante** comprender esto:

* El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.
* El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.

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

Cuando una aplicación se crea para usar una cuenta de B2C individual (`IndividualB2C`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

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
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.

La configuración se suministra a través del archivo `wwwroot/appsettings.json`:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Ejemplo:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
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

Para más información, vea las siguientes secciones del artículo *Otros escenarios*:

* [Solicitar tokens de acceso adicionales](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Adjuntar tokens a solicitudes salientes](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Modo de inicio de sesión

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente App

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede hacer lo siguiente:

* Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.
* Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
