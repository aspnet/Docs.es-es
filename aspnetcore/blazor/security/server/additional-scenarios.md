---
title: Otros escenarios de seguridad de Blazor Server en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar Blazor Server en otros escenarios de seguridad.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
ms.openlocfilehash: 16896577c56c638cae6824c58f4d3a6ce1489458
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014014"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="38d1b-103">Otros escenarios de seguridad de Blazor Server en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38d1b-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="38d1b-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="38d1b-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="38d1b-105">Transmisión de tokens a una aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="38d1b-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="38d1b-106">Con el método descrito en esta sección, los tokens disponibles fuera de los componentes Razor de una aplicación Blazor Server se pueden pasar a otros componentes.</span><span class="sxs-lookup"><span data-stu-id="38d1b-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="38d1b-107">Para obtener código de ejemplo, incluido un ejemplo de `Startup.ConfigureServices` completo, vea [Paso de tokens a una aplicación Blazor del lado servidor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="38d1b-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="38d1b-108">Autentique la aplicación Blazor Server como lo haría con una instancia de Razor Pages o aplicación MVC al uso.</span><span class="sxs-lookup"><span data-stu-id="38d1b-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="38d1b-109">Aprovisione y guarde los tokens en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="38d1b-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="38d1b-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="38d1b-110">For example:</span></span>

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

<span data-ttu-id="38d1b-111">Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:</span><span class="sxs-lookup"><span data-stu-id="38d1b-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="38d1b-112">Defina un servicio de proveedor de tokens **con ámbito** que se pueda usar en la aplicación Blazor para resolver los tokens procedentes de [inserciones de dependencias](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="38d1b-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="38d1b-113">En `Startup.ConfigureServices`, agregue servicios para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="38d1b-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="38d1b-114">En el archivo `_Host.cshtml`, cree una instancia de `InitialApplicationState` y pásela como parámetro a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="38d1b-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="38d1b-115">En el componente `App` (`App.razor`), resuelva el servicio e inicialícelo con los datos del parámetro:</span><span class="sxs-lookup"><span data-stu-id="38d1b-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="38d1b-116">En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para llamar a la API:</span><span class="sxs-lookup"><span data-stu-id="38d1b-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="38d1b-117">Establecimiento del esquema de autenticación</span><span class="sxs-lookup"><span data-stu-id="38d1b-117">Set the authentication scheme</span></span>

<span data-ttu-id="38d1b-118">En una aplicación que usa más de un middleware de autenticación (y que, por tanto, tiene más de un esquema de autenticación), el esquema que Blazor usa se puede establecer explícitamente en la configuración del punto de conexión de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="38d1b-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="38d1b-119">En el siguiente ejemplo se establece el esquema de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="38d1b-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="38d1b-120">Uso de puntos de conexión de OpenID Connect (OIDC) v2.0</span><span class="sxs-lookup"><span data-stu-id="38d1b-120">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="38d1b-121">La biblioteca de autenticación y las plantillas de Blazor usan puntos de conexión de OpenID Connect (OIDC) v1.0.</span><span class="sxs-lookup"><span data-stu-id="38d1b-121">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="38d1b-122">Para usar un punto de conexión de la versión 2.0, configure la opción <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="38d1b-122">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="38d1b-123">Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="38d1b-123">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="38d1b-124">Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente.</span><span class="sxs-lookup"><span data-stu-id="38d1b-124">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="38d1b-125">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o en el archivo de configuración de la aplicación con la clave <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.</span><span class="sxs-lookup"><span data-stu-id="38d1b-125">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="38d1b-126">Cambios en el código</span><span class="sxs-lookup"><span data-stu-id="38d1b-126">Code changes</span></span>

* <span data-ttu-id="38d1b-127">La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0.</span><span class="sxs-lookup"><span data-stu-id="38d1b-127">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="38d1b-128">Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="38d1b-128">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="38d1b-129">en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="38d1b-129">in the Azure documentation.</span></span>
* <span data-ttu-id="38d1b-130">Dado que los recursos de los puntos de conexión de la versión 2.0 se especifican en URI de ámbito, quite el valor de la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="38d1b-130">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

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

<span data-ttu-id="38d1b-131">Encontrará el URI del identificador de aplicación que se va a usar en la descripción del registro de la aplicación del proveedor de OIDC.</span><span class="sxs-lookup"><span data-stu-id="38d1b-131">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
