---
title: Otros escenarios de seguridad de Blazor Server en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar Blazor Server en otros escenarios de seguridad.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
ms.openlocfilehash: 89288f3fce2dbb6f2647693ba8aaf29500b5bb2b
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805497"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="f092c-103">Otros escenarios de seguridad de Blazor Server en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f092c-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="f092c-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f092c-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="f092c-105">Transmisión de tokens a una aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="f092c-105">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="f092c-106">Con el método descrito en esta sección, los tokens disponibles fuera de los componentes Razor de una aplicación Blazor Server se pueden pasar a otros componentes.</span><span class="sxs-lookup"><span data-stu-id="f092c-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="f092c-107">Autentique la aplicación Blazor Server como lo haría con una instancia de Razor Pages o aplicación MVC al uso.</span><span class="sxs-lookup"><span data-stu-id="f092c-107">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="f092c-108">Aprovisione y guarde los tokens en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f092c-108">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="f092c-109">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f092c-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="f092c-110">Opcionalmente, se agregan ámbitos adicionales con `options.Scope.Add("{SCOPE}");`, donde el marcador de posición `{SCOPE}` es el ámbito adicional que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="f092c-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="f092c-111">Defina un servicio de proveedor de tokens **con ámbito** que se pueda usar en la aplicación Blazor para resolver los tokens procedentes de [inserciones de dependencias](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="f092c-111">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="f092c-112">En `Startup.ConfigureServices`, agregue servicios para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f092c-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="f092c-113">Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:</span><span class="sxs-lookup"><span data-stu-id="f092c-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="f092c-114">En el archivo `_Host.cshtml`, cree una instancia de `InitialApplicationState` y pásela como parámetro a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f092c-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="f092c-115">En el componente `App` (`App.razor`), resuelva el servicio e inicialícelo con los datos del parámetro:</span><span class="sxs-lookup"><span data-stu-id="f092c-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="f092c-116">Agregue una referencia de paquete a la aplicación para el paquete de NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).</span><span class="sxs-lookup"><span data-stu-id="f092c-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="f092c-117">En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para la solicitud de API:</span><span class="sxs-lookup"><span data-stu-id="f092c-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="f092c-118">Establecimiento del esquema de autenticación</span><span class="sxs-lookup"><span data-stu-id="f092c-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="f092c-119">En una aplicación que usa más de un middleware de autenticación (y que, por tanto, tiene más de un esquema de autenticación), el esquema que Blazor usa se puede establecer explícitamente en la configuración del punto de conexión de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f092c-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="f092c-120">En el siguiente ejemplo se establece el esquema de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="f092c-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="f092c-121">Transmisión de tokens a una aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="f092c-121">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="f092c-122">Con el método descrito en esta sección, los tokens disponibles fuera de los componentes Razor de una aplicación Blazor Server se pueden pasar a otros componentes.</span><span class="sxs-lookup"><span data-stu-id="f092c-122">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="f092c-123">Autentique la aplicación Blazor Server como lo haría con una instancia de Razor Pages o aplicación MVC al uso.</span><span class="sxs-lookup"><span data-stu-id="f092c-123">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="f092c-124">Aprovisione y guarde los tokens en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f092c-124">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="f092c-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f092c-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="f092c-126">Opcionalmente, se agregan ámbitos adicionales con `options.Scope.Add("{SCOPE}");`, donde el marcador de posición `{SCOPE}` es el ámbito adicional que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="f092c-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="f092c-127">Opcionalmente, el recurso se especifica con `options.Resource = "{RESOURCE}";`, donde el marcador de posición `{RESOURCE}` es el recurso.</span><span class="sxs-lookup"><span data-stu-id="f092c-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="f092c-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f092c-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="f092c-129">Defina una clase para pasar el estado de la aplicación inicial con los tokens de acceso y actualización:</span><span class="sxs-lookup"><span data-stu-id="f092c-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="f092c-130">Defina un servicio de proveedor de tokens **con ámbito** que se pueda usar en la aplicación Blazor para resolver los tokens procedentes de [inserciones de dependencias](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="f092c-130">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="f092c-131">En `Startup.ConfigureServices`, agregue servicios para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f092c-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="f092c-132">En el archivo `_Host.cshtml`, cree una instancia de `InitialApplicationState` y pásela como parámetro a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f092c-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="f092c-133">En el componente `App` (`App.razor`), resuelva el servicio e inicialícelo con los datos del parámetro:</span><span class="sxs-lookup"><span data-stu-id="f092c-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="f092c-134">Agregue una referencia de paquete a la aplicación para el paquete de NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).</span><span class="sxs-lookup"><span data-stu-id="f092c-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="f092c-135">En el servicio que realiza una solicitud de API segura, inserte el proveedor de tokens y recupere el token para la solicitud de API:</span><span class="sxs-lookup"><span data-stu-id="f092c-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="f092c-136">Establecimiento del esquema de autenticación</span><span class="sxs-lookup"><span data-stu-id="f092c-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="f092c-137">En una aplicación que usa más de un middleware de autenticación (y que, por tanto, tiene más de un esquema de autenticación), el esquema que Blazor usa se puede establecer explícitamente en la configuración del punto de conexión de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f092c-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="f092c-138">En el siguiente ejemplo se establece el esquema de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="f092c-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="f092c-139">Uso de puntos de conexión de OpenID Connect (OIDC) v2.0</span><span class="sxs-lookup"><span data-stu-id="f092c-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="f092c-140">En las versiones de ASP.NET Core anteriores a la 5.0, la biblioteca de autenticación y las plantillas de Blazor usan puntos de conexión de OpenID Connect (OIDC) v1.0.</span><span class="sxs-lookup"><span data-stu-id="f092c-140">In versions of ASP.NET Core prior to 5.0, the authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="f092c-141">Para usar un punto de conexión v2.0 con versiones de ASP.NET Core anteriores a la 5.0, configure la opción <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="f092c-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="f092c-142">Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="f092c-142">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="f092c-143">Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente.</span><span class="sxs-lookup"><span data-stu-id="f092c-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="f092c-144">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o en el archivo de configuración de la aplicación con la clave <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.</span><span class="sxs-lookup"><span data-stu-id="f092c-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="f092c-145">Cambios en el código</span><span class="sxs-lookup"><span data-stu-id="f092c-145">Code changes</span></span>

* <span data-ttu-id="f092c-146">La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0.</span><span class="sxs-lookup"><span data-stu-id="f092c-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="f092c-147">Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="f092c-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="f092c-148">en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="f092c-148">in the Azure documentation.</span></span>
* <span data-ttu-id="f092c-149">Dado que los recursos de los puntos de conexión de la versión 2.0 se especifican en URI de ámbito, quite el valor de la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> en <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="f092c-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="f092c-150">Para obtener más información, vea [Ámbitos, no recursos](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="f092c-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="f092c-151">URI de id. de aplicación</span><span class="sxs-lookup"><span data-stu-id="f092c-151">App ID URI</span></span>

* <span data-ttu-id="f092c-152">Cuando se usan puntos de conexión 2.0, las API definen un elemento *`App ID URI`* , que está pensado a fin de representar un identificador único para la API.</span><span class="sxs-lookup"><span data-stu-id="f092c-152">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="f092c-153">Todos los ámbitos incluyen el URI del id. de aplicación como prefijo, y los puntos de conexión de la versión 2.0 emiten tokens de acceso con el URI del id. de aplicación como audiencia.</span><span class="sxs-lookup"><span data-stu-id="f092c-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="f092c-154">Cuando se usan puntos de conexión de la versión 2.0, el id. de cliente configurado en Server API cambia del id. de aplicación de la API (id. de cliente) al URI del id. de aplicación.</span><span class="sxs-lookup"><span data-stu-id="f092c-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="f092c-155">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f092c-155">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="f092c-156">Encontrará el URI del identificador de aplicación que se va a usar en la descripción del registro de la aplicación del proveedor de OIDC.</span><span class="sxs-lookup"><span data-stu-id="f092c-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
