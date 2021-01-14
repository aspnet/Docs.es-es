---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a4f3234aa4b4b02244d17615a9033db3094d3580
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024787"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*

Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), siga las instrucciones para sus herramientas elegidas.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:

1. Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.

1. Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code y CLI de .NET Core](#tab/visual-studio-code+netcore-cli)

Cree un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía. Especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core:

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

1. La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [Identity](xref:security/authentication/identity).

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

La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de OpenID Connect (OIDC). El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC. Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC. Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La plantilla Blazor WebAssembly configura automáticamente los ámbitos predeterminados para `openid` y `profile`.

La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:

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
