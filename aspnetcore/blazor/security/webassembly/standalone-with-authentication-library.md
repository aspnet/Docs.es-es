---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280887"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación

*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*

Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), siga las instrucciones para sus herramientas elegidas. Si va a agregar compatibilidad con la autenticación, consulte las siguientes secciones de este artículo para obtener instrucciones sobre cómo instalar y configurar la aplicación.

> [!NOTE]
> El proveedor (IP) Identity debe usar [OpenID Connect (OIDC)](https://openid.net/connect/). Por ejemplo, la dirección IP de Facebook no es un proveedor compatible con OIDC, por lo que las instrucciones de este tema no funcionan con la dirección IP de Facebook. Para obtener más información, vea <xref:blazor/security/webassembly/index#authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:

1. Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.

1. Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para usar el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core. Esta selección agrega compatibilidad con la autenticación y no conlleva el almacenamiento de usuarios en una base de datos. En las siguientes secciones de este artículo se proporcionan más detalles.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code y CLI de .NET Core](#tab/visual-studio-code+netcore-cli)

Cree un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía. Especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para usar el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core. Esta selección agrega compatibilidad con la autenticación y no conlleva el almacenamiento de usuarios en una base de datos. En las siguientes secciones de este artículo se proporcionan más detalles.

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| Marcador de posición  | Ejemplo        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:

1. En el paso **Configure your new Blazor WebAssembly App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).

1. La aplicación se crea para usar [Identity](xref:security/authentication/identity) de ASP.NET Core y no conlleva el almacenamiento de usuarios en una base de datos. En las siguientes secciones de este artículo se proporcionan más detalles.

---

## <a name="authentication-package"></a>Paquete de autenticación

Cuando una aplicación se crea para usar cuentas de usuario individuales, dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

## <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> proporcionado por el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.

Para una nueva aplicación, proporcione valores para los marcadores de posición `{AUTHORITY}` y `{CLIENT ID}` en la siguiente configuración. Proporcione otros valores de configuración que sean necesarios para su uso con la dirección IP de la aplicación. El ejemplo es para Google, que requiere `PostLogoutRedirectUri`, `RedirectUri` y `ResponseType`. Si incluye autenticación en una aplicación, agregue manualmente el código y la configuración siguientes a la aplicación con valores para los marcadores de posición y otros valores de configuración.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

La configuración se suministra a través del archivo `wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Ejemplo OIDC de Google OAuth 2.0:

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

El URI de redireccionamiento (`https://localhost:5001/authentication/login-callback`) se registra en la [consola de API de Google](https://console.developers.google.com/apis/dashboard) en **Credenciales** >  **`{NAME}`**  > **URIs de redireccionamiento autorizados**, donde `{NAME}` es el nombre de cliente de la aplicación en la lista **Identificadores de cliente de OAuth 2.0** de la consola de API de Google.

La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de OpenID Connect (OIDC). El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC. Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC. Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La plantilla Blazor WebAssembly configura automáticamente los ámbitos predeterminados para `openid` y `profile`.

La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>. Si incluye autenticación en una aplicación, agregue manualmente el código siguiente y configure el URI de ámbito.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

Para más información, vea las siguientes secciones del artículo *Otros escenarios*:

* [Solicitar tokens de acceso adicionales](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Adjuntar tokens a solicitudes salientes](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>Componente App

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:

* En el caso de los usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Proporciona un botón para cerrar la sesión de la aplicación.
* En el caso de los usuarios anónimos, ofrece la posibilidad de iniciar sesión.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:
  * Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.
  * Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.
