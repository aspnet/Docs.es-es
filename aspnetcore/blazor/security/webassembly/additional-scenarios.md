---
title: Otros escenarios de seguridad de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly en otros escenarios de seguridad.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 4e7f7c89e7dbc1851069b6e7024065e96495a317
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402187"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="f12ad-103">Otros escenarios de seguridad de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f12ad-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="f12ad-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f12ad-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="f12ad-105">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="f12ad-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="f12ad-106">El servicio <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> se puede usar con <xref:System.Net.Http.HttpClient> para adjuntar tokens de acceso a solicitudes salientes.</span><span class="sxs-lookup"><span data-stu-id="f12ad-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="f12ad-107">Los tokens se obtienen usando el servicio <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> existente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="f12ad-108">Si no se puede obtener un token, se produce una excepción <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="f12ad-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> tiene un método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> que se puede usar para llevar al usuario al proveedor de identidades para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="f12ad-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="f12ad-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> se puede configurar con las direcciones URL, los ámbitos y la dirección URL de retorno autorizados usando el método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="f12ad-111">Siga cualquiera de los métodos siguientes para configurar un controlador de mensajes para solicitudes salientes:</span><span class="sxs-lookup"><span data-stu-id="f12ad-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="f12ad-112">[Clase `AuthorizationMessageHandler` personalizada](#custom-authorizationmessagehandler-class) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="f12ad-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="f12ad-113">Configurar `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="f12ad-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="f12ad-114">Clase AuthorizationMessageHandler personalizada</span><span class="sxs-lookup"><span data-stu-id="f12ad-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="f12ad-115">En el siguiente ejemplo, una clase personalizada amplía <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>, que se puede usar para configurar un cliente <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="f12ad-116">En `Program.Main` (`Program.cs`), un cliente <xref:System.Net.Http.HttpClient> se configura con el controlador de mensajes de autorización personalizado:</span><span class="sxs-lookup"><span data-stu-id="f12ad-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="f12ad-117">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="f12ad-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="f12ad-118">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)), se proporcionan instancias al cliente <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor:</span><span class="sxs-lookup"><span data-stu-id="f12ad-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="f12ad-119">Configurar AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="f12ad-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="f12ad-120">En el siguiente ejemplo, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un cliente <xref:System.Net.Http.HttpClient> en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="f12ad-121">Para mayor comodidad, se incluye un controlador <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ya preconfigurado con la dirección base de la aplicación como dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="f12ad-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="f12ad-122">Ahora, las plantillas de Blazor WebAssembly habilitadas para la autenticación usan <xref:System.Net.Http.IHttpClientFactory> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)) en el proyecto de API de servidor para configurar un cliente <xref:System.Net.Http.HttpClient> con el controlador <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="f12ad-123">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> en el ejemplo anterior, se proporcionan instancias al cliente <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="f12ad-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f12ad-124">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):</span><span class="sxs-lookup"><span data-stu-id="f12ad-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="f12ad-125">HttpClient con tipo</span><span class="sxs-lookup"><span data-stu-id="f12ad-125">Typed HttpClient</span></span>

<span data-ttu-id="f12ad-126">Se puede definir un cliente con tipo que controle todos los problemas de obtención de tokens y HTTP dentro de una misma clase.</span><span class="sxs-lookup"><span data-stu-id="f12ad-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="f12ad-127">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="f12ad-127">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="f12ad-128">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="f12ad-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="f12ad-129">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-129">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="f12ad-130">Componente `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-130">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="f12ad-131">Configurar el controlador HttpClient</span><span class="sxs-lookup"><span data-stu-id="f12ad-131">Configure the HttpClient handler</span></span>

<span data-ttu-id="f12ad-132">El controlador se puede seguir configurando con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="f12ad-132">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="f12ad-133">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-133">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="f12ad-134">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="f12ad-134">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="f12ad-135">Si la aplicación Blazor WebAssembly usa por lo general un cliente <xref:System.Net.Http.HttpClient> predeterminado seguro, la aplicación también podrá realizar solicitudes de API web no autenticadas o no autorizadas configurando un cliente <xref:System.Net.Http.HttpClient> con nombre:</span><span class="sxs-lookup"><span data-stu-id="f12ad-135">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="f12ad-136">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-136">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="f12ad-137">El registro anterior se realiza además del registro de <xref:System.Net.Http.HttpClient> predeterminado seguro existente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-137">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="f12ad-138">Un componente crea el cliente <xref:System.Net.Http.HttpClient> a partir del cliente <xref:System.Net.Http.IHttpClientFactory> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="f12ad-138">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="f12ad-139">El controlador de la API de servidor (`WeatherForecastNoAuthenticationController` en el ejemplo anterior) no está marcado con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="f12ad-139">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="f12ad-140">La decisión de si usar un cliente seguro o uno no seguro como la instancia de <xref:System.Net.Http.HttpClient> predeterminada depende del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="f12ad-140">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="f12ad-141">Una manera de tomar esta decisión es tener en cuenta el número de puntos de conexión autenticados y no autenticados en los que la aplicación establece el contacto.</span><span class="sxs-lookup"><span data-stu-id="f12ad-141">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="f12ad-142">Si la mayoría de las solicitudes de la aplicación es para proteger los puntos de conexión de la API, use la instancia de <xref:System.Net.Http.HttpClient> autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f12ad-142">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="f12ad-143">De lo contrario, registre la instancia de <xref:System.Net.Http.HttpClient> no autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f12ad-143">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="f12ad-144">Un enfoque alternativo al uso de <xref:System.Net.Http.IHttpClientFactory> es crear un [cliente con tipo](#typed-httpclient) para el acceso no autenticado a puntos de conexión anónimos.</span><span class="sxs-lookup"><span data-stu-id="f12ad-144">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="f12ad-145">Solicitar más tokens de acceso</span><span class="sxs-lookup"><span data-stu-id="f12ad-145">Request additional access tokens</span></span>

<span data-ttu-id="f12ad-146">Los tokens de acceso se pueden obtener manualmente llamando a `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-146">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="f12ad-147">En el siguiente ejemplo, una aplicación necesita más ámbitos de la API de Microsoft Graph de Azure Active Directory (AAD) para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-147">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="f12ad-148">Tras agregar los permisos de la API de Microsoft Graph en el portal de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-148">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="f12ad-149">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-149">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="f12ad-150">El método `IAccessTokenProvider.RequestToken` proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un determinado conjunto de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="f12ad-150">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="f12ad-151">En un componente Razor:</span><span class="sxs-lookup"><span data-stu-id="f12ad-151">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="f12ad-152"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> devuelve lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f12ad-152"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="f12ad-153">`true` con el `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="f12ad-153">`true` with the `token` for use.</span></span>
* <span data-ttu-id="f12ad-154">`false` si el token no se obtiene.</span><span class="sxs-lookup"><span data-stu-id="f12ad-154">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="f12ad-155">HttpClient y HttpRequestMessage con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="f12ad-155">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="f12ad-156">Al ejecutar WebAssembly en una aplicación Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) y <xref:System.Net.Http.HttpRequestMessage> se pueden usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f12ad-156">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="f12ad-157">Por ejemplo, se puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="f12ad-157">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="f12ad-158">El siguiente componente realiza una solicitud `POST` a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="f12ad-158">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="f12ad-159">En la implementación de <xref:System.Net.Http.HttpClient> de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="f12ad-159">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="f12ad-160">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="f12ad-160">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="f12ad-161">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión <xref:System.Net.Http.HttpRequestMessage> que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="f12ad-161">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="f12ad-162">Método de extensión</span><span class="sxs-lookup"><span data-stu-id="f12ad-162">Extension method</span></span> | <span data-ttu-id="f12ad-163">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="f12ad-163">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="f12ad-164">Se pueden establecer más opciones usando el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="f12ad-164">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="f12ad-165">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación Blazor WebAssembly para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f12ad-165">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="f12ad-166">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f12ad-166">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="f12ad-167">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-167">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="f12ad-168">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="f12ad-168">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="f12ad-169">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-169">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="f12ad-170">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="f12ad-170">The following policy includes configuration for:</span></span>

* <span data-ttu-id="f12ad-171">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="f12ad-171">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="f12ad-172">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="f12ad-172">Any method (verb).</span></span>
* <span data-ttu-id="f12ad-173">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-173">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="f12ad-174">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-174">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="f12ad-175">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="f12ad-175">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="f12ad-176">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="f12ad-176">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="f12ad-177">Controlar los errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="f12ad-177">Handle token request errors</span></span>

<span data-ttu-id="f12ad-178">Cuando una aplicación de página única autentica a un usuario a través de Open ID Connect (OIDC), el estado de autenticación se conserva localmente en dicha aplicación de página única y también en el proveedor de Identity en forma de cookie de sesión que se establece como consecuencia de que un usuario haya especificado sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="f12ad-178">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="f12ad-179">Normalmente, los tokens que el proveedor de identidades emite para el usuario son válidos durante un breve período de tiempo (aproximadamente una hora), por lo que la aplicación cliente debe capturar nuevos tokens de manera regular.</span><span class="sxs-lookup"><span data-stu-id="f12ad-179">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="f12ad-180">De lo contrario, se cerrará la sesión del usuario después de que los tokens concedidos expiren.</span><span class="sxs-lookup"><span data-stu-id="f12ad-180">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="f12ad-181">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario tenga que autenticarse otra vez, gracias al estado de autenticación o a la "sesión" que el proveedor de identidades conserva.</span><span class="sxs-lookup"><span data-stu-id="f12ad-181">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="f12ad-182">Hay algunos casos en los que el cliente no puede obtener un token sin interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión del proveedor de identidades expresamente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-182">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="f12ad-183">Este escenario sucede si un usuario visita `https://login.microsoftonline.com` y cierra sesión. En estos casos, la aplicación no sabe de inmediato que el usuario ha cerrado la sesión. Es posible que el token que el cliente contiene ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="f12ad-183">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="f12ad-184">Además, el cliente no puede aprovisionar un token nuevo sin interacción del usuario después de que el token actual expire.</span><span class="sxs-lookup"><span data-stu-id="f12ad-184">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="f12ad-185">Estos escenarios no son específicos de la autenticación basada en tokens,</span><span class="sxs-lookup"><span data-stu-id="f12ad-185">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="f12ad-186">sino que forman parte de la idiosincrasia de las aplicaciones de página única.</span><span class="sxs-lookup"><span data-stu-id="f12ad-186">They are part of the nature of SPAs.</span></span> <span data-ttu-id="f12ad-187">Una aplicación de página única que use cookies tampoco podrá llamar a una API de servidor si la cookie de autenticación se quita.</span><span class="sxs-lookup"><span data-stu-id="f12ad-187">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="f12ad-188">Cuando una aplicación realiza llamadas API a recursos protegidos, hay que tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f12ad-188">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="f12ad-189">Para aprovisionar un nuevo token de acceso para llamar a la API, puede que el usuario deba autenticarse de nuevo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-189">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="f12ad-190">Aun cuando el cliente tenga un token que parezca válido, puede que se produzca un error en la llamada al servidor porque el usuario ha revocado dicho token.</span><span class="sxs-lookup"><span data-stu-id="f12ad-190">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="f12ad-191">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="f12ad-191">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="f12ad-192">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="f12ad-192">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="f12ad-193">La solicitud no se realiza correctamente y la aplicación debe volver a autenticar al usuario para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="f12ad-193">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="f12ad-194">Cuando una solicitud de token no se realiza correctamente, debe decidir si quiere guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="f12ad-194">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="f12ad-195">Para ello, existen varios métodos con niveles de complejidad crecientes:</span><span class="sxs-lookup"><span data-stu-id="f12ad-195">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="f12ad-196">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-196">Store the current page state in session storage.</span></span> <span data-ttu-id="f12ad-197">Durante el [evento de ciclo de vida `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="f12ad-197">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="f12ad-198">Agregar un parámetro de cadena de consulta y usarlo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-198">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="f12ad-199">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión, sin riesgo de que se produzcan discrepancias con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="f12ad-199">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="f12ad-200">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="f12ad-200">The following example shows how to:</span></span>

* <span data-ttu-id="f12ad-201">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-201">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="f12ad-202">Recuperar el estado anterior tras la autenticación usando el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="f12ad-202">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="f12ad-203">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="f12ad-203">Save app state before an authentication operation</span></span>

<span data-ttu-id="f12ad-204">Durante una operación de autenticación, hay casos en los que conviene guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="f12ad-204">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="f12ad-205">Esto puede suceder cuando se usa un contenedor de estado y se quiere restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-205">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="f12ad-206">Se puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a este, y restaurar el estado después de que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-206">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="f12ad-207">Este método se describe en el siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-207">The following example demonstrates the approach.</span></span>

<span data-ttu-id="f12ad-208">Se crea una clase de contenedor de estado en la aplicación con propiedades que contienen los valores de estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f12ad-208">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="f12ad-209">En el siguiente ejemplo, el contenedor se usa para mantener el valor de contador del componente `Counter` de la plantilla predeterminada (`Pages/Counter.razor`).</span><span class="sxs-lookup"><span data-stu-id="f12ad-209">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="f12ad-210">Los métodos para serializar y deserializar el contenedor se basan en <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-210">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="f12ad-211">El componente `Counter` usa el contenedor de estado para conservar el valor de `currentCount` fuera del componente:</span><span class="sxs-lookup"><span data-stu-id="f12ad-211">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="f12ad-212">Cree un objeto `ApplicationAuthenticationState` a partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-212">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="f12ad-213">Proporcione una propiedad `Id`, que actúa como identificador del estado almacenado localmente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-213">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="f12ad-214">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="f12ad-214">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="f12ad-215">El componente `Authentication` (`Pages/Authentication.razor`) guarda y restaura el estado de la aplicación usando el almacenamiento de sesión local con los métodos de serialización y deserialización de `StateContainer`, `GetStateForLocalStorage` y `SetStateFromLocalStorage`:</span><span class="sxs-lookup"><span data-stu-id="f12ad-215">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="f12ad-216">En este ejemplo se usa Azure Active Directory (AAD) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="f12ad-216">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="f12ad-217">En `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-217">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="f12ad-218">El objeto `ApplicationAuthenticationState` se configura como el tipo `RemoteAuthenticationState` de la Biblioteca de autenticación de Microsoft (MSAL).</span><span class="sxs-lookup"><span data-stu-id="f12ad-218">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="f12ad-219">El contenedor de estado se registra en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f12ad-219">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="f12ad-220">Personalizar rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="f12ad-220">Customize app routes</span></span>

<span data-ttu-id="f12ad-221">La biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa de forma predeterminada las rutas indicadas en la siguiente tabla para representar distintos estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f12ad-221">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="f12ad-222">Ruta</span><span class="sxs-lookup"><span data-stu-id="f12ad-222">Route</span></span>                            | <span data-ttu-id="f12ad-223">Propósito</span><span class="sxs-lookup"><span data-stu-id="f12ad-223">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="f12ad-224">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-224">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="f12ad-225">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-225">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="f12ad-226">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-226">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="f12ad-227">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-227">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="f12ad-228">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-228">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="f12ad-229">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="f12ad-229">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="f12ad-230">Indica que el usuario ha cerrado sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-230">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="f12ad-231">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="f12ad-231">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="f12ad-232">Desencadena una operación para registrar un usuario nuevo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-232">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="f12ad-233">Las rutas que se muestran en la tabla anterior se pueden configurar a través de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-233">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f12ad-234">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="f12ad-234">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="f12ad-235">En el siguiente ejemplo, todas las rutas de acceso tienen el prefijo `/security`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-235">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="f12ad-236">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-236">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f12ad-237">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-237">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="f12ad-238">Si el requisito precisa de rutas de acceso completamente diferentes, establezca las rutas tal y como se ha descrito anteriormente y represente la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="f12ad-238">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="f12ad-239">La interfaz de usuario se puede fragmentar en diferentes páginas si así decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-239">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="f12ad-240">Personalizar la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="f12ad-240">Customize the authentication user interface</span></span>

<span data-ttu-id="f12ad-241"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> incluye un conjunto predeterminado de fragmentos de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f12ad-241"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="f12ad-242">Cada estado se puede personalizar pasando un objeto <xref:Microsoft.AspNetCore.Components.RenderFragment> personalizado.</span><span class="sxs-lookup"><span data-stu-id="f12ad-242">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="f12ad-243">Para personalizar el texto que aparece durante el proceso de inicio de sesión inicial, puede cambiar la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> como se indica aquí.</span><span class="sxs-lookup"><span data-stu-id="f12ad-243">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="f12ad-244">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-244">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f12ad-245">La vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tiene un fragmento que se puede usar por cada ruta de autenticación que se muestra en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="f12ad-245">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="f12ad-246">Ruta</span><span class="sxs-lookup"><span data-stu-id="f12ad-246">Route</span></span>                            | <span data-ttu-id="f12ad-247">Fragmento</span><span class="sxs-lookup"><span data-stu-id="f12ad-247">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="f12ad-248">Personalizar el usuario</span><span class="sxs-lookup"><span data-stu-id="f12ad-248">Customize the user</span></span>

<span data-ttu-id="f12ad-249">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="f12ad-249">Users bound to the app can be customized.</span></span> <span data-ttu-id="f12ad-250">En el siguiente ejemplo, todos los usuarios autenticados reciben una notificación `amr` por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="f12ad-250">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="f12ad-251">Cree una clase que extienda la clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-251">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="f12ad-252">Cree una fábrica que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-252">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="f12ad-253">Registre la fábrica `CustomAccountFactory` del proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="f12ad-253">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="f12ad-254">Cualquiera de los siguientes registros es válido:</span><span class="sxs-lookup"><span data-stu-id="f12ad-254">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="f12ad-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="f12ad-256"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-256"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="f12ad-257"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-257"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="f12ad-258">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="f12ad-258">Support prerendering with authentication</span></span>

<span data-ttu-id="f12ad-259">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación hospedada Blazor WebAssembly, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="f12ad-259">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="f12ad-260">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="f12ad-260">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="f12ad-261">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="f12ad-261">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="f12ad-262">En la clase `Program` de la aplicación cliente (`Program.cs`), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-262">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="f12ad-263">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="f12ad-263">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="f12ad-264">En el método `Startup.Configure` de la aplicación de servidor, reemplace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="f12ad-264">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="f12ad-265">En la aplicación de servidor, cree una carpeta `Pages`, si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="f12ad-265">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="f12ad-266">Cree una página `_Host.cshtml` dentro de la carpeta `Pages` de la aplicación de Server.</span><span class="sxs-lookup"><span data-stu-id="f12ad-266">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="f12ad-267">Pegue el contenido del archivo `wwwroot/index.html` de la aplicación cliente en el archivo `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-267">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="f12ad-268">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="f12ad-268">Update the file's contents:</span></span>

* <span data-ttu-id="f12ad-269">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="f12ad-269">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="f12ad-270">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f12ad-270">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="f12ad-271">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="f12ad-271">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="f12ad-272">Al autenticar y autorizar una aplicación Blazor WebAssembly con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="f12ad-272">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="f12ad-273">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="f12ad-273">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="f12ad-274">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f12ad-274">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="f12ad-275">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="f12ad-275">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="f12ad-276">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="f12ad-276">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="f12ad-277">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="f12ad-277">In this scenario:</span></span>

* <span data-ttu-id="f12ad-278">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="f12ad-278">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="f12ad-279">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="f12ad-279">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="f12ad-280">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="f12ad-280">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="f12ad-281">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="f12ad-281">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="f12ad-282">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="f12ad-282">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="f12ad-283">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="f12ad-283">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="f12ad-284">Cuando un usuario inicia sesión, Identity recopila los tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f12ad-284">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="f12ad-285">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="f12ad-285">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="f12ad-286">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="f12ad-286">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="f12ad-287">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="f12ad-287">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="f12ad-288">A partir de ahí, use el token de acceso de terceros para llamar directamente a los recursos de la API de terceros desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-288">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="f12ad-289">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="f12ad-289">We don't recommend this approach.</span></span> <span data-ttu-id="f12ad-290">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="f12ad-290">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="f12ad-291">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="f12ad-291">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="f12ad-292">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="f12ad-292">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="f12ad-293">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="f12ad-293">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="f12ad-294">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="f12ad-294">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="f12ad-295">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="f12ad-295">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="f12ad-296">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="f12ad-296">Make an API call from the client to the server API.</span></span> <span data-ttu-id="f12ad-297">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="f12ad-297">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="f12ad-298">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="f12ad-298">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="f12ad-299">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="f12ad-299">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="f12ad-300">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="f12ad-300">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="f12ad-301">Usar puntos de conexión de Open ID Connect (OIDC) versión 2.0</span><span class="sxs-lookup"><span data-stu-id="f12ad-301">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="f12ad-302">La Biblioteca de autenticación y las plantillas de Blazor usan puntos de conexión la versión 1.0 de Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="f12ad-302">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="f12ad-303">Para usar un punto de conexión de la versión 2.0, configure la opción <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> del portador de JWT.</span><span class="sxs-lookup"><span data-stu-id="f12ad-303">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="f12ad-304">En el siguiente ejemplo, AAD se ha configurado para la versión 2.0 anexando un segmento `v2.0` a la propiedad <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="f12ad-304">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="f12ad-305">Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="f12ad-305">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="f12ad-306">Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente.</span><span class="sxs-lookup"><span data-stu-id="f12ad-306">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="f12ad-307">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o en el archivo de configuración de la aplicación (`appsettings.json`) con la clave `Authority`.</span><span class="sxs-lookup"><span data-stu-id="f12ad-307">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="f12ad-308">La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0.</span><span class="sxs-lookup"><span data-stu-id="f12ad-308">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="f12ad-309">Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="f12ad-309">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
