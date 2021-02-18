---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory.
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 21d6e6fd9859cf4daf28e0bc3cf70562192844dd
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280926"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory

En este artículo se explica cómo crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> En las aplicaciones de Blazor WebAssembly creadas en Visual Studio que se han configurado para admitir cuentas de un directorio organizativo de AAD, Visual Studio no configura actualmente los registros de aplicaciones de Azure Portal correctamente durante la generación del proyecto. Esto se va a corregir en una próxima versión de Visual Studio.
>
> En este artículo se muestra cómo crear la solución y el registro del portal de aplicaciones de Azure con el comando de la CLI de .NET `dotnet new` y mediante la creación manual del registro de aplicaciones en Azure Portal.
>
> Si prefiere crear la solución y el registro de aplicaciones de Azure con Visual Studio antes de que se actualice el IDE, consulte **_cada sección de este artículo_** y confirme o actualice la configuración de las aplicaciones y el registro de aplicaciones después de que Visual Studio cree la solución.

Registre una aplicación de AAD en el área **Azure Active Directory** > **Registros de aplicaciones** de Azure Portal:

1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Standalone AAD**).
1. Elija un **tipo de cuenta compatible**. En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo**.
1. Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.

Registre la siguiente información:

* Identificador de aplicación (cliente); por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.
* Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.

En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Registre una aplicación de AAD en el área **Azure Active Directory** > **Registros de aplicaciones** de Azure Portal:

1. Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Standalone AAD**).
1. Elija un **tipo de cuenta compatible**. En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo**.
1. Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación. En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**. Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección. Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.
1. Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.

Registre la siguiente información:

* Identificador de aplicación (cliente); por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.
* Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.

En **Autenticación** > **Configuraciones de plataforma** > **Web**:

1. Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.
1. En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**
1. Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.
1. Seleccione el botón **Guardar**.

::: moniker-end

Cree la aplicación en una carpeta vacía. Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Marcador de posición   | Nombre de Azure Portal       | Ejemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Id. de aplicación (cliente) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | Id. de directorio (inquilino)   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

> [!NOTE]
> En Azure Portal, el valor de configuración de plataforma **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.
>
> Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.
>
> Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en Azure Portal y actualice el URI de redirección con el puerto correcto.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

Después de crear la aplicación, debe poder hacer lo siguiente:

* Iniciar sesión en la aplicación con una cuenta de usuario de AAD
* Solicitar tokens de acceso para API de Microsoft Para obtener más información, consulte:
  * [Ámbitos de token de acceso](#access-token-scopes)
  * [Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Paquete de autenticación

Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a la aplicación de forma transitiva.

## <a name="authentication-service-support"></a>Compatibilidad con el servicio de autenticación

La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal). Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación. Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD al registrar la aplicación.

La configuración se suministra a través del archivo `wwwroot/appsettings.json`:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Ejemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>Ámbitos de token de acceso

La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura. Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

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

## <a name="login-mode"></a>Modo de inicio de sesión

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a>Componente App

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/webassembly/additional-scenarios>
* [Creación de una versión personalizada de la biblioteca de JavaScript Authentication.MSAL](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentación de la plataforma de identidad de Microsoft](/azure/active-directory/develop/)
