---
title: Otros escenarios de seguridad de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar Blazor WebAssembly en otros escenarios de seguridad.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: e1f7e8b85537f0671451d9975487645a1c005e74
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626290"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="6eb6b-103">Otros escenarios de seguridad de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eb6b-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="6eb6b-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6eb6b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="6eb6b-105">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="6eb6b-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="6eb6b-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> es un elemento <xref:System.Net.Http.DelegatingHandler> que se usa para asociar los tokens de acceso a las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="6eb6b-107">Los tokens se adquieren mediante el servicio <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>, registrado por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="6eb6b-108">Si no se puede obtener un token, se produce una excepción <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="6eb6b-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> tiene un método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> que se puede usar para llevar al usuario al proveedor de identidades para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="6eb6b-110">Para mayor comodidad, el marco de trabajo proporciona el elemento <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigurado con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="6eb6b-111">**Los tokens de acceso solo se agregan cuando el URI de solicitud se encuentra dentro del URI base de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="6eb6b-112">Cuando los URI de solicitud de salida no están dentro del URI base de la aplicación, use una [clase `AuthorizationMessageHandler` personalizada (*recomendado*)](#custom-authorizationmessagehandler-class) o [configure `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="6eb6b-113">Además de la configuración de la aplicación cliente para el acceso a la API del servidor, la API del servidor también debe permitir solicitudes entre orígenes (CORS) cuando el cliente y el servidor no residen en la misma dirección base.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="6eb6b-114">Para obtener más información sobre la configuración de CORS del lado servidor, consulte la sección [Uso compartido de recursos entre orígenes (CORS)](#cross-origin-resource-sharing-cors) más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="6eb6b-115">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-115">In the following example:</span></span>

* <span data-ttu-id="6eb6b-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> agrega <xref:System.Net.Http.IHttpClientFactory> y los servicios relacionados a la colección de servicios y configura un objeto <xref:System.Net.Http.HttpClient> con nombre (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="6eb6b-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> es la dirección base del URI del recurso cuando se envían solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="6eb6b-118"><xref:System.Net.Http.IHttpClientFactory> lo proporciona el paquete de NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="6eb6b-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> es el elemento <xref:System.Net.Http.DelegatingHandler> que se usa para asociar los tokens de acceso a las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="6eb6b-120">Los tokens de acceso solo se agregan cuando el URI de solicitud se encuentra dentro del URI base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="6eb6b-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> crea y configura una instancia de <xref:System.Net.Http.HttpClient> para las solicitudes salientes mediante la configuración que corresponde al elemento denominado <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="6eb6b-122">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, los URI de solicitud se encuentran en el URI base de la aplicación de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="6eb6b-123">Por lo tanto, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> en una aplicación generada a partir de la plantilla del proyecto.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="6eb6b-124">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="6eb6b-125">Clase `AuthorizationMessageHandler` personalizada</span><span class="sxs-lookup"><span data-stu-id="6eb6b-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="6eb6b-126">*Las instrucciones de esta sección se recomiendan para las aplicaciones cliente que realizan solicitudes salientes a los URI que no están dentro del URI base de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="6eb6b-127">En el ejemplo siguiente, una clase personalizada extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> para su uso como <xref:System.Net.Http.DelegatingHandler> de un <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="6eb6b-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura este controlador para autorizar las solicitudes HTTP salientes mediante un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="6eb6b-129">El token de acceso solo se adjunta si al menos una de las direcciones URL autorizadas es una base del URI de solicitud (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="6eb6b-130">En `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` se registra como un servicio con ámbito y se configura como <xref:System.Net.Http.DelegatingHandler> para las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes realizadas por un elemento llamado <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="6eb6b-131">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="6eb6b-132">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="6eb6b-133">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)), se proporcionan instancias al cliente <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="6eb6b-134">Si el URI de solicitud es un URI relativo, como en el ejemplo siguiente (`ExampleAPIMethod`), se combina con el elemento <xref:System.Net.Http.HttpClient.BaseAddress> cuando la aplicación cliente realiza la solicitud:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="6eb6b-135">Configuración de `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="6eb6b-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="6eb6b-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> se puede configurar con las direcciones URL, los ámbitos y una dirección URL de retorno autorizados usando el método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="6eb6b-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura el controlador para autorizar las solicitudes HTTP salientes mediante un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="6eb6b-138">El token de acceso solo se adjunta si al menos una de las direcciones URL autorizadas es una base del URI de solicitud (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="6eb6b-139">Si el URI de solicitud es un URI relativo, se combina con el elemento <xref:System.Net.Http.HttpClient.BaseAddress>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="6eb6b-140">En el siguiente ejemplo, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un cliente <xref:System.Net.Http.HttpClient> en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="6eb6b-141">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se asigna al elemento siguiente de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="6eb6b-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="6eb6b-143">Dirección URL de la matriz de `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="6eb6b-144">Con tipo `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="6eb6b-144">Typed `HttpClient`</span></span>

<span data-ttu-id="6eb6b-145">Se puede definir un cliente con tipo que controle todos los problemas de obtención de tokens y HTTP dentro de una misma clase.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="6eb6b-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="6eb6b-147">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="6eb6b-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="6eb6b-149">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="6eb6b-150">Componente `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="6eb6b-151">Configuración del controlador `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="6eb6b-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="6eb6b-152">El controlador se puede seguir configurando con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="6eb6b-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="6eb6b-154">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se asigna al elemento siguiente de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="6eb6b-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="6eb6b-156">Dirección URL de la matriz de `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="6eb6b-157">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="6eb6b-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="6eb6b-158">Si la aplicación Blazor WebAssembly usa por lo general un cliente <xref:System.Net.Http.HttpClient> predeterminado seguro, la aplicación también podrá realizar solicitudes de API web no autenticadas o no autorizadas configurando un cliente <xref:System.Net.Http.HttpClient> con nombre:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="6eb6b-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="6eb6b-160">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="6eb6b-161">El registro anterior se realiza además del registro de <xref:System.Net.Http.HttpClient> predeterminado seguro existente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="6eb6b-162">Un componente crea el cliente <xref:System.Net.Http.HttpClient> a partir del cliente <xref:System.Net.Http.IHttpClientFactory> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="6eb6b-163">El controlador de la API de servidor (`WeatherForecastNoAuthenticationController` en el ejemplo anterior) no está marcado con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="6eb6b-164">La decisión de si usar un cliente seguro o uno no seguro como la instancia de <xref:System.Net.Http.HttpClient> predeterminada depende del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="6eb6b-165">Una manera de tomar esta decisión es tener en cuenta el número de puntos de conexión autenticados y no autenticados en los que la aplicación establece el contacto.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="6eb6b-166">Si la mayoría de las solicitudes de la aplicación es para proteger los puntos de conexión de la API, use la instancia de <xref:System.Net.Http.HttpClient> autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="6eb6b-167">De lo contrario, registre la instancia de <xref:System.Net.Http.HttpClient> no autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="6eb6b-168">Un enfoque alternativo al uso de <xref:System.Net.Http.IHttpClientFactory> es crear un [cliente con tipo](#typed-httpclient) para el acceso no autenticado a puntos de conexión anónimos.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="6eb6b-169">Solicitar más tokens de acceso</span><span class="sxs-lookup"><span data-stu-id="6eb6b-169">Request additional access tokens</span></span>

<span data-ttu-id="6eb6b-170">Los tokens de acceso se pueden obtener manualmente llamando a `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="6eb6b-171">En el siguiente ejemplo, una aplicación necesita más ámbitos de la API de Microsoft Graph de Azure Active Directory (AAD) para leer los datos de usuario y enviar correo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-171">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="6eb6b-172">Tras agregar los permisos de la API de Microsoft Graph en el portal de Azure AAD, los ámbitos adicionales se configuran en la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-172">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="6eb6b-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-173">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="6eb6b-174">El método `IAccessTokenProvider.RequestToken` proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un determinado conjunto de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-174">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="6eb6b-175">En un componente Razor:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-175">In a Razor component:</span></span>

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

<span data-ttu-id="6eb6b-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> devuelve lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="6eb6b-177">`true` con el `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-177">`true` with the `token` for use.</span></span>
* <span data-ttu-id="6eb6b-178">`false` si el token no se obtiene.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-178">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="6eb6b-179">`HttpClient` y `HttpRequestMessage` con las opciones de solicitud de Fetch API</span><span class="sxs-lookup"><span data-stu-id="6eb6b-179">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="6eb6b-180">Al ejecutar WebAssembly en una aplicación Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) ([documentación de API](xref:System.Net.Http.HttpClient)) y <xref:System.Net.Http.HttpRequestMessage> se pueden usar para personalizar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-180">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="6eb6b-181">Por ejemplo, se puede especificar el método HTTP y los encabezados de solicitud.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-181">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="6eb6b-182">El siguiente componente realiza una solicitud `POST` a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-182">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="6eb6b-183">En la implementación de <xref:System.Net.Http.HttpClient> de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-183">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="6eb6b-184">Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-184">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="6eb6b-185">Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión <xref:System.Net.Http.HttpRequestMessage> que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-185">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="6eb6b-186">Método de extensión</span><span class="sxs-lookup"><span data-stu-id="6eb6b-186">Extension method</span></span> | <span data-ttu-id="6eb6b-187">Propiedad de solicitud de captura</span><span class="sxs-lookup"><span data-stu-id="6eb6b-187">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="6eb6b-188">Se pueden establecer más opciones usando el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>, que es más genérico.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-188">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="6eb6b-189">Normalmente, la respuesta HTTP se almacena en búfer en una aplicación Blazor WebAssembly para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-189">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="6eb6b-190">Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-190">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="6eb6b-191">Para incluir credenciales en una solicitud entre orígenes, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-191">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="6eb6b-192">Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-192">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="6eb6b-193">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="6eb6b-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="6eb6b-194">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-194">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="6eb6b-195">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-195">The following policy includes configuration for:</span></span>

* <span data-ttu-id="6eb6b-196">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-196">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="6eb6b-197">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-197">Any method (verb).</span></span>
* <span data-ttu-id="6eb6b-198">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-198">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="6eb6b-199">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-199">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="6eb6b-200">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-200">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="6eb6b-201">Una solución hospedada Blazor basada en la plantilla del proyecto hospedada Blazor utiliza la misma dirección base para las aplicaciones cliente y servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-201">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="6eb6b-202">De forma predeterminada, el elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de la aplicación cliente está establecido en un URI de `builder.HostEnvironment.BaseAddress`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-202">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="6eb6b-203">La configuración de CORS **no** es necesaria en la configuración predeterminada de una aplicación hospedada creada a partir de la plantilla del proyecto hospedada de Blazor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-203">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="6eb6b-204">Las aplicaciones cliente adicionales que no se hospedan en el proyecto de servidor y no comparten la dirección base de la aplicación de servidor **no** requieren la configuración de CORS en el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-204">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="6eb6b-205">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-205">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="6eb6b-206">Controlar los errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="6eb6b-206">Handle token request errors</span></span>

<span data-ttu-id="6eb6b-207">Cuando una aplicación de página única autentica a un usuario a través de OpenID Connect (OIDC), el estado de autenticación se conserva localmente en dicha aplicación de página única y también en el proveedor de Identity en forma de cookie de sesión que se establece como consecuencia de que un usuario haya especificado sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-207">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="6eb6b-208">Normalmente, los tokens que el proveedor de identidades emite para el usuario son válidos durante un breve período de tiempo (aproximadamente una hora), por lo que la aplicación cliente debe capturar nuevos tokens de manera regular.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-208">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="6eb6b-209">De lo contrario, se cerrará la sesión del usuario después de que los tokens concedidos expiren.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-209">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="6eb6b-210">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario tenga que autenticarse otra vez, gracias al estado de autenticación o a la "sesión" que el proveedor de identidades conserva.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-210">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="6eb6b-211">Hay algunos casos en los que el cliente no puede obtener un token sin interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión del proveedor de identidades expresamente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-211">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="6eb6b-212">Este escenario sucede si un usuario visita `https://login.microsoftonline.com` y cierra sesión. En estos casos, la aplicación no sabe de inmediato que el usuario ha cerrado la sesión. Es posible que el token que el cliente contiene ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-212">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="6eb6b-213">Además, el cliente no puede aprovisionar un token nuevo sin interacción del usuario después de que el token actual expire.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-213">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="6eb6b-214">Estos escenarios no son específicos de la autenticación basada en tokens,</span><span class="sxs-lookup"><span data-stu-id="6eb6b-214">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="6eb6b-215">sino que forman parte de la idiosincrasia de las aplicaciones de página única.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-215">They are part of the nature of SPAs.</span></span> <span data-ttu-id="6eb6b-216">Una aplicación de página única que use cookies tampoco podrá llamar a una API de servidor si la cookie de autenticación se quita.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-216">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="6eb6b-217">Cuando una aplicación realiza llamadas API a recursos protegidos, hay que tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-217">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="6eb6b-218">Para aprovisionar un nuevo token de acceso para llamar a la API, puede que el usuario deba autenticarse de nuevo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-218">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="6eb6b-219">Aun cuando el cliente tenga un token que parezca válido, puede que se produzca un error en la llamada al servidor porque el usuario ha revocado dicho token.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-219">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="6eb6b-220">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-220">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="6eb6b-221">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-221">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="6eb6b-222">La solicitud no se realiza correctamente y la aplicación debe volver a autenticar al usuario para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-222">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="6eb6b-223">Cuando una solicitud de token no se realiza correctamente, debe decidir si quiere guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-223">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="6eb6b-224">Para ello, existen varios métodos con niveles de complejidad crecientes:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-224">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="6eb6b-225">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-225">Store the current page state in session storage.</span></span> <span data-ttu-id="6eb6b-226">Durante el [evento de ciclo de vida `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-226">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="6eb6b-227">Agregar un parámetro de cadena de consulta y usarlo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-227">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="6eb6b-228">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión, sin riesgo de que se produzcan discrepancias con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-228">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="6eb6b-229">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-229">The following example shows how to:</span></span>

* <span data-ttu-id="6eb6b-230">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-230">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="6eb6b-231">Recuperar el estado anterior tras la autenticación usando el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-231">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="6eb6b-232">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="6eb6b-232">Save app state before an authentication operation</span></span>

<span data-ttu-id="6eb6b-233">Durante una operación de autenticación, hay casos en los que conviene guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-233">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="6eb6b-234">Esto puede suceder cuando se usa un contenedor de estado y se quiere restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-234">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="6eb6b-235">Se puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a este, y restaurar el estado después de que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-235">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="6eb6b-236">Este método se describe en el siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-236">The following example demonstrates the approach.</span></span>

<span data-ttu-id="6eb6b-237">Se crea una clase de contenedor de estado en la aplicación con propiedades que contienen los valores de estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-237">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="6eb6b-238">En el siguiente ejemplo, el contenedor se usa para mantener el valor de contador del componente `Counter` de la plantilla del proyecto predeterminada (`Pages/Counter.razor`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-238">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="6eb6b-239">Los métodos para serializar y deserializar el contenedor se basan en <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-239">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="6eb6b-240">El componente `Counter` usa el contenedor de estado para conservar el valor de `currentCount` fuera del componente:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-240">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="6eb6b-241">Cree un objeto `ApplicationAuthenticationState` a partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-241">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="6eb6b-242">Proporcione una propiedad `Id`, que actúa como identificador del estado almacenado localmente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-242">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="6eb6b-243">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-243">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="6eb6b-244">El componente `Authentication` (`Pages/Authentication.razor`) guarda y restaura el estado de la aplicación usando el almacenamiento de sesión local con los métodos de serialización y deserialización de `StateContainer`, `GetStateForLocalStorage` y `SetStateFromLocalStorage`:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-244">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="6eb6b-245">En este ejemplo se usa Azure Active Directory (AAD) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-245">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="6eb6b-246">En `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-246">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="6eb6b-247">El objeto `ApplicationAuthenticationState` se configura como el tipo `RemoteAuthenticationState` de la Biblioteca de autenticación de Microsoft (MSAL).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-247">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="6eb6b-248">El contenedor de estado se registra en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-248">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="6eb6b-249">Personalizar rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="6eb6b-249">Customize app routes</span></span>

<span data-ttu-id="6eb6b-250">La biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) usa de forma predeterminada las rutas indicadas en la siguiente tabla para representar distintos estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-250">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="6eb6b-251">Ruta</span><span class="sxs-lookup"><span data-stu-id="6eb6b-251">Route</span></span>                            | <span data-ttu-id="6eb6b-252">Propósito</span><span class="sxs-lookup"><span data-stu-id="6eb6b-252">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="6eb6b-253">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-253">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="6eb6b-254">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-254">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="6eb6b-255">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-255">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="6eb6b-256">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-256">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="6eb6b-257">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-257">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="6eb6b-258">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-258">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="6eb6b-259">Indica que el usuario ha cerrado sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-259">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="6eb6b-260">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-260">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="6eb6b-261">Desencadena una operación para registrar un usuario nuevo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-261">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="6eb6b-262">Las rutas que se muestran en la tabla anterior se pueden configurar a través de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-262">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6eb6b-263">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-263">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="6eb6b-264">En el siguiente ejemplo, todas las rutas de acceso tienen el prefijo `/security`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-264">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="6eb6b-265">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-265">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="6eb6b-266">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-266">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="6eb6b-267">Si el requisito precisa de rutas de acceso completamente diferentes, establezca las rutas tal y como se ha descrito anteriormente y represente la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-267">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="6eb6b-268">La interfaz de usuario se puede fragmentar en diferentes páginas si así decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-268">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="6eb6b-269">Personalizar la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="6eb6b-269">Customize the authentication user interface</span></span>

<span data-ttu-id="6eb6b-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> incluye un conjunto predeterminado de fragmentos de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="6eb6b-271">Cada estado se puede personalizar pasando un objeto <xref:Microsoft.AspNetCore.Components.RenderFragment> personalizado.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-271">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="6eb6b-272">Para personalizar el texto que aparece durante el proceso de inicio de sesión inicial, puede cambiar la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> como se indica aquí.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-272">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="6eb6b-273">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-273">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="6eb6b-274">La vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tiene un fragmento que se puede usar por cada ruta de autenticación que se muestra en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-274">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="6eb6b-275">Ruta</span><span class="sxs-lookup"><span data-stu-id="6eb6b-275">Route</span></span>                            | <span data-ttu-id="6eb6b-276">Fragmento</span><span class="sxs-lookup"><span data-stu-id="6eb6b-276">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="6eb6b-277">Personalizar el usuario</span><span class="sxs-lookup"><span data-stu-id="6eb6b-277">Customize the user</span></span>

<span data-ttu-id="6eb6b-278">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-278">Users bound to the app can be customized.</span></span> <span data-ttu-id="6eb6b-279">En el siguiente ejemplo, todos los usuarios autenticados reciben una notificación `amr` por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-279">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="6eb6b-280">Cree una clase que extienda la clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-280">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="6eb6b-281">Cree una fábrica que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-281">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="6eb6b-282">Registre la fábrica `CustomAccountFactory` del proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-282">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="6eb6b-283">Cualquiera de los siguientes registros es válido:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-283">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="6eb6b-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="6eb6b-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="6eb6b-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="6eb6b-287">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="6eb6b-287">Support prerendering with authentication</span></span>

<span data-ttu-id="6eb6b-288">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación hospedada Blazor WebAssembly, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-288">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="6eb6b-289">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-289">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="6eb6b-290">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-290">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="6eb6b-291">En la clase `Program` de la aplicación cliente (`Program.cs`), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-291">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

<span data-ttu-id="6eb6b-292">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-292">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="6eb6b-293">En el método `Startup.Configure` de la aplicación de servidor, reemplace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-293">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="6eb6b-294">En la aplicación de servidor, cree una carpeta `Pages`, si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-294">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="6eb6b-295">Cree una página `_Host.cshtml` dentro de la carpeta `Pages` de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-295">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="6eb6b-296">Pegue el contenido del archivo `wwwroot/index.html` de la aplicación cliente en el archivo `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-296">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="6eb6b-297">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-297">Update the file's contents:</span></span>

* <span data-ttu-id="6eb6b-298">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-298">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="6eb6b-299">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-299">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="6eb6b-300">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="6eb6b-300">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="6eb6b-301">Al autenticar y autorizar una aplicación Blazor WebAssembly con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-301">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="6eb6b-302">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-302">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="6eb6b-303">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-303">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="6eb6b-304">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="6eb6b-304">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="6eb6b-305">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-305">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="6eb6b-306">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-306">In this scenario:</span></span>

* <span data-ttu-id="6eb6b-307">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-307">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="6eb6b-308">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-308">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="6eb6b-309">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-309">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="6eb6b-310">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="6eb6b-310">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="6eb6b-311">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-311">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="6eb6b-312">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-312">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="6eb6b-313">Cuando un usuario inicia sesión, Identity recopila los tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-313">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="6eb6b-314">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-314">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="6eb6b-315">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="6eb6b-315">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="6eb6b-316">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-316">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="6eb6b-317">A partir de ahí, use el token de acceso de terceros para llamar directamente a los recursos de la API de terceros desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-317">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="6eb6b-318">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-318">We don't recommend this approach.</span></span> <span data-ttu-id="6eb6b-319">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-319">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="6eb6b-320">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-320">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="6eb6b-321">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-321">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="6eb6b-322">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-322">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="6eb6b-323">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-323">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="6eb6b-324">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="6eb6b-324">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="6eb6b-325">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-325">Make an API call from the client to the server API.</span></span> <span data-ttu-id="6eb6b-326">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-326">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="6eb6b-327">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-327">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="6eb6b-328">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-328">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="6eb6b-329">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-329">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="6eb6b-330">Uso de puntos de conexión de OpenID Connect (OIDC) v2.0</span><span class="sxs-lookup"><span data-stu-id="6eb6b-330">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="6eb6b-331">La biblioteca de autenticación y las plantillas de proyecto Blazor usan puntos de conexión de OpenID Connect (OIDC) v1.0.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-331">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="6eb6b-332">Para usar un punto de conexión de la versión 2.0, configure la opción <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> del portador de JWT.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-332">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="6eb6b-333">En el siguiente ejemplo, AAD se ha configurado para la versión 2.0 anexando un segmento `v2.0` a la propiedad <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="6eb6b-333">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="6eb6b-334">Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-334">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="6eb6b-335">Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-335">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="6eb6b-336">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o en el archivo de configuración de la aplicación (`appsettings.json`) con la clave `Authority`.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-336">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="6eb6b-337">La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-337">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="6eb6b-338">Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-338">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="6eb6b-339">Configuración y uso de gRPC en componentes</span><span class="sxs-lookup"><span data-stu-id="6eb6b-339">Configure and use gRPC in components</span></span>

<span data-ttu-id="6eb6b-340">Para configurar una aplicación Blazor WebAssembly que use el [marco gRPC de ASP.NET Core](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-340">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="6eb6b-341">Habilite gRPC-Web en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-341">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="6eb6b-342">Para obtener más información, vea <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-342">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="6eb6b-343">Registre los servicios de gRPC para el controlador de mensajes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-343">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="6eb6b-344">En el ejemplo siguiente se configura el controlador de mensajes de autorización de la aplicación para usar el servicio [`GreeterClient` del tutorial de gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-344">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="6eb6b-345">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-345">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="6eb6b-346">Coloque el archivo de `.proto` en el proyecto `Shared` de la solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-346">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="6eb6b-347">Un componente de la aplicación cliente puede realizar llamadas a gRPC mediante el cliente de gRPC (`Pages/Grpc.razor`):</span><span class="sxs-lookup"><span data-stu-id="6eb6b-347">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="6eb6b-348">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6eb6b-348">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="6eb6b-349">Para usar la propiedad `Status.DebugException`, use [GRPC.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), versión 2.30.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-349">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="6eb6b-350">Para obtener más información, vea <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="6eb6b-350">For more information, see <xref:grpc/browser>.</span></span>
