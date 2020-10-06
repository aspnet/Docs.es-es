---
title: Otros escenarios de seguridad de Blazor Server en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar Blazor Server en otros escenarios de seguridad.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393670"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>Otros escenarios de seguridad de Blazor Server en ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a>Transmisión de tokens a una aplicación Blazor Server

Con el método descrito en esta sección, los tokens disponibles fuera de los componentes Razor de una aplicación Blazor Server se pueden pasar a otros componentes. Para obtener código de ejemplo, incluido un ejemplo de `Startup.ConfigureServices` completo, vea [Paso de tokens a una aplicación Blazor del lado servidor](https://github.com/javiercn/blazor-server-aad-sample).

Autentique la aplicación Blazor Server como lo haría con una instancia de Razor Pages o aplicación MVC al uso. Aprovisione y guarde los tokens en la cookie de autenticación. Por ejemplo:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Defina un servicio de proveedor de tokens **con ámbito** que se pueda usar en la aplicación Blazor para resolver los tokens procedentes de [inserciones de dependencias](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

En `Startup.ConfigureServices`, agregue servicios para lo siguiente:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

En el archivo `_Host.cshtml`, cree una instancia de `InitialApplicationState` y pásela como parámetro a la aplicación:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

En el componente `App` (`App.razor`), resuelva el servicio e inicialícelo con los datos del parámetro:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Agregue una referencia de paquete a la aplicación para el paquete NuGet [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).

En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para llamar a la API:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a>Establecimiento del esquema de autenticación

En una aplicación que usa más de un middleware de autenticación (y que, por tanto, tiene más de un esquema de autenticación), el esquema que Blazor usa se puede establecer explícitamente en la configuración del punto de conexión de `Startup.Configure`. En el siguiente ejemplo se establece el esquema de Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Uso de puntos de conexión de OpenID Connect (OIDC) v2.0

La biblioteca de autenticación y las plantillas de Blazor usan puntos de conexión de OpenID Connect (OIDC) v1.0. Para usar un punto de conexión de la versión 2.0, configure la opción <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente. Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o en el archivo de configuración de la aplicación con la clave <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.

### <a name="code-changes"></a>Cambios en el código

* La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0. Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) en la documentación de Azure.
* Dado que los recursos de los puntos de conexión de la versión 2.0 se especifican en URI de ámbito, quite el valor de la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Para obtener más información, vea [Ámbitos, no recursos](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) en la documentación de Azure.

### <a name="app-id-uri"></a>URI de id. de aplicación

* Cuando se usan puntos de conexión 2.0, las API definen un elemento *`App ID URI`* , que está pensado a fin de representar un identificador único para la API.
* Todos los ámbitos incluyen el URI del id. de aplicación como prefijo, y los puntos de conexión de la versión 2.0 emiten tokens de acceso con el URI del id. de aplicación como audiencia.
* Cuando se usan puntos de conexión de la versión 2.0, el id. de cliente configurado en Server API cambia del id. de aplicación de la API (id. de cliente) al URI del id. de aplicación.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Encontrará el URI del identificador de aplicación que se va a usar en la descripción del registro de la aplicación del proveedor de OIDC.
