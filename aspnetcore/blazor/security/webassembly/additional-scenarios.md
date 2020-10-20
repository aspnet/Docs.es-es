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
ms.openlocfilehash: 50d455b37c51fdd6d3b52b10b3e819eb45526de4
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900965"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="06ed3-103">Otros escenarios de seguridad de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06ed3-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="06ed3-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="06ed3-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="06ed3-105">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="06ed3-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="06ed3-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> es un elemento <xref:System.Net.Http.DelegatingHandler> que se usa para asociar los tokens de acceso a las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes.</span><span class="sxs-lookup"><span data-stu-id="06ed3-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="06ed3-107">Los tokens se adquieren mediante el servicio <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>, registrado por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="06ed3-108">Si no se puede obtener un token, se produce una excepción <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="06ed3-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> tiene un método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> que se puede usar para llevar al usuario al proveedor de identidades para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="06ed3-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="06ed3-110">Para mayor comodidad, el marco de trabajo proporciona el elemento <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigurado con la dirección base de la aplicación como una dirección URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="06ed3-111">**Los tokens de acceso solo se agregan cuando el URI de solicitud se encuentra dentro del URI base de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="06ed3-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="06ed3-112">Cuando los URI de solicitud de salida no están dentro del URI base de la aplicación, use una [clase `AuthorizationMessageHandler` personalizada (*recomendado*)](#custom-authorizationmessagehandler-class) o [configure `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="06ed3-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="06ed3-113">Además de la configuración de la aplicación cliente para el acceso a la API del servidor, la API del servidor también debe permitir solicitudes entre orígenes (CORS) cuando el cliente y el servidor no residen en la misma dirección base.</span><span class="sxs-lookup"><span data-stu-id="06ed3-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="06ed3-114">Para obtener más información sobre la configuración de CORS del lado servidor, consulte la sección [Uso compartido de recursos entre orígenes (CORS)](#cross-origin-resource-sharing-cors) más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="06ed3-115">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-115">In the following example:</span></span>

* <span data-ttu-id="06ed3-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> agrega <xref:System.Net.Http.IHttpClientFactory> y los servicios relacionados a la colección de servicios y configura un objeto <xref:System.Net.Http.HttpClient> con nombre (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="06ed3-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> es la dirección base del URI del recurso cuando se envían solicitudes.</span><span class="sxs-lookup"><span data-stu-id="06ed3-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="06ed3-118"><xref:System.Net.Http.IHttpClientFactory> lo proporciona el paquete de NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).</span><span class="sxs-lookup"><span data-stu-id="06ed3-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="06ed3-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> es el elemento <xref:System.Net.Http.DelegatingHandler> que se usa para asociar los tokens de acceso a las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes.</span><span class="sxs-lookup"><span data-stu-id="06ed3-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="06ed3-120">Los tokens de acceso solo se agregan cuando el URI de solicitud se encuentra dentro del URI base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="06ed3-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> crea y configura una instancia de <xref:System.Net.Http.HttpClient> para las solicitudes salientes mediante la configuración que corresponde al elemento denominado <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

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

<span data-ttu-id="06ed3-122">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, los URI de solicitud se encuentran en el URI base de la aplicación de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="06ed3-123">Por lo tanto, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> en una aplicación generada a partir de la plantilla del proyecto.</span><span class="sxs-lookup"><span data-stu-id="06ed3-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="06ed3-124">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):</span><span class="sxs-lookup"><span data-stu-id="06ed3-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="06ed3-125">Clase `AuthorizationMessageHandler` personalizada</span><span class="sxs-lookup"><span data-stu-id="06ed3-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="06ed3-126">*Las instrucciones de esta sección se recomiendan para las aplicaciones cliente que realizan solicitudes salientes a los URI que no están dentro del URI base de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="06ed3-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="06ed3-127">En el ejemplo siguiente, una clase personalizada extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> para su uso como <xref:System.Net.Http.DelegatingHandler> de un <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="06ed3-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura este controlador para autorizar las solicitudes HTTP salientes mediante un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="06ed3-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="06ed3-129">El token de acceso solo se adjunta si al menos una de las direcciones URL autorizadas es una base del URI de solicitud (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="06ed3-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="06ed3-130">En `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` se registra como un servicio con ámbito y se configura como <xref:System.Net.Http.DelegatingHandler> para las instancias de <xref:System.Net.Http.HttpResponseMessage> salientes realizadas por un elemento llamado <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="06ed3-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="06ed3-131">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="06ed3-132">El cliente <xref:System.Net.Http.HttpClient> configurado se usa para realizar solicitudes autorizadas por medio del patrón [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="06ed3-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="06ed3-133">Cuando el cliente se crea con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)), se proporcionan instancias al cliente <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="06ed3-134">Si el URI de solicitud es un URI relativo, como en el ejemplo siguiente (`ExampleAPIMethod`), se combina con el elemento <xref:System.Net.Http.HttpClient.BaseAddress> cuando la aplicación cliente realiza la solicitud:</span><span class="sxs-lookup"><span data-stu-id="06ed3-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="06ed3-135">Configuración de `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="06ed3-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="06ed3-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> se puede configurar con las direcciones URL, los ámbitos y una dirección URL de retorno autorizados usando el método <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="06ed3-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura el controlador para autorizar las solicitudes HTTP salientes mediante un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="06ed3-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="06ed3-138">El token de acceso solo se adjunta si al menos una de las direcciones URL autorizadas es una base del URI de solicitud (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="06ed3-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="06ed3-139">Si el URI de solicitud es un URI relativo, se combina con el elemento <xref:System.Net.Http.HttpClient.BaseAddress>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="06ed3-140">En el siguiente ejemplo, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un cliente <xref:System.Net.Http.HttpClient> en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="06ed3-141">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se asigna al elemento siguiente de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="06ed3-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="06ed3-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="06ed3-143">Dirección URL de la matriz de `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-143">A URL of the `authorizedUrls` array.</span></span>

### <a name="graph-api-example"></a><span data-ttu-id="06ed3-144">Ejemplo de Graph API</span><span class="sxs-lookup"><span data-stu-id="06ed3-144">Graph API example</span></span>

<span data-ttu-id="06ed3-145">En el ejemplo siguiente, se usa un <xref:System.Net.Http.HttpClient> con nombre para Graph API para obtener el número de teléfono móvil de un usuario para procesar una llamada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-145">In the following example, a named <xref:System.Net.Http.HttpClient> for Graph API is used to obtain a user's mobile phone number to process a call.</span></span> <span data-ttu-id="06ed3-146">Después de agregar el permiso `User.Read` de Microsoft Graph API en el área de AAD de Azure Portal, el ámbito se configura para el cliente con nombre en la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-146">After adding the Microsoft Graph API `User.Read` permission in the AAD area of the Azure portal, the scope is configured for the named client in the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>

> [!NOTE]
> <span data-ttu-id="06ed3-147">En el ejemplo de esta sección se obtienen datos de Graph API para el usuario en el *código del componente*.</span><span class="sxs-lookup"><span data-stu-id="06ed3-147">The example in this section obtains Graph API data for the user in *component code*.</span></span> <span data-ttu-id="06ed3-148">Para crear notificaciones de usuario a partir de Graph API, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="06ed3-148">To create user claims from Graph API, see the following resources:</span></span>
>
> * <span data-ttu-id="06ed3-149">Sección [Personalizar el usuario](#customize-the-user)</span><span class="sxs-lookup"><span data-stu-id="06ed3-149">[Customize the user](#customize-the-user) section</span></span>
> * <xref:blazor/security/webassembly/aad-groups-roles>

<span data-ttu-id="06ed3-150">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-150">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="06ed3-151">En `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-151">In `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="06ed3-152">En un componente Razor (`Pages/CallUser.razor`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-152">In a Razor component (`Pages/CallUser.razor`):</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="06ed3-153">En el ejemplo anterior, el desarrollador implementa el `ICallProcessor` personalizado (`CallProcessor`) en la cola y luego realiza llamadas automatizadas.</span><span class="sxs-lookup"><span data-stu-id="06ed3-153">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="06ed3-154">Con tipo `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="06ed3-154">Typed `HttpClient`</span></span>

<span data-ttu-id="06ed3-155">Se puede definir un cliente con tipo que controle todos los problemas de obtención de tokens y HTTP dentro de una misma clase.</span><span class="sxs-lookup"><span data-stu-id="06ed3-155">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="06ed3-156">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-156">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="06ed3-157">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-157">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="06ed3-158">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-158">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="06ed3-159">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-159">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="06ed3-160">Componente `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="06ed3-161">Configuración del controlador `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="06ed3-161">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="06ed3-162">El controlador se puede seguir configurando con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="06ed3-162">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="06ed3-163">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-163">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="06ed3-164">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se asigna al elemento siguiente de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="06ed3-164">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="06ed3-165"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-165">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="06ed3-166">Dirección URL de la matriz de `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-166">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="06ed3-167">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="06ed3-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="06ed3-168">Si la aplicación Blazor WebAssembly usa por lo general un cliente <xref:System.Net.Http.HttpClient> predeterminado seguro, la aplicación también podrá realizar solicitudes de API web no autenticadas o no autorizadas configurando un cliente <xref:System.Net.Http.HttpClient> con nombre:</span><span class="sxs-lookup"><span data-stu-id="06ed3-168">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="06ed3-169">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-169">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="06ed3-170">En el caso de una aplicación Blazor basada en la plantilla del proyecto hospedada Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) se asigna al elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-170">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="06ed3-171">El registro anterior se realiza además del registro de <xref:System.Net.Http.HttpClient> predeterminado seguro existente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-171">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="06ed3-172">Un componente crea el cliente <xref:System.Net.Http.HttpClient> a partir del cliente <xref:System.Net.Http.IHttpClientFactory> (paquete [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) para realizar solicitudes no autenticadas o no autorizadas:</span><span class="sxs-lookup"><span data-stu-id="06ed3-172">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="06ed3-173">El controlador de la API de servidor (`WeatherForecastNoAuthenticationController` en el ejemplo anterior) no está marcado con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="06ed3-173">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="06ed3-174">La decisión de si usar un cliente seguro o uno no seguro como la instancia de <xref:System.Net.Http.HttpClient> predeterminada depende del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="06ed3-174">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="06ed3-175">Una manera de tomar esta decisión es tener en cuenta el número de puntos de conexión autenticados y no autenticados en los que la aplicación establece el contacto.</span><span class="sxs-lookup"><span data-stu-id="06ed3-175">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="06ed3-176">Si la mayoría de las solicitudes de la aplicación es para proteger los puntos de conexión de la API, use la instancia de <xref:System.Net.Http.HttpClient> autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="06ed3-176">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="06ed3-177">De lo contrario, registre la instancia de <xref:System.Net.Http.HttpClient> no autenticada como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="06ed3-177">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="06ed3-178">Un enfoque alternativo al uso de <xref:System.Net.Http.IHttpClientFactory> es crear un [cliente con tipo](#typed-httpclient) para el acceso no autenticado a puntos de conexión anónimos.</span><span class="sxs-lookup"><span data-stu-id="06ed3-178">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="06ed3-179">Solicitar más tokens de acceso</span><span class="sxs-lookup"><span data-stu-id="06ed3-179">Request additional access tokens</span></span>

<span data-ttu-id="06ed3-180">Los tokens de acceso se pueden obtener manualmente llamando a `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-180">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="06ed3-181">En el ejemplo siguiente, una aplicación requiere un ámbito adicional para el <xref:System.Net.Http.HttpClient>predeterminado.</span><span class="sxs-lookup"><span data-stu-id="06ed3-181">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="06ed3-182">En el ejemplo de la biblioteca de autenticación de Microsoft (MSAL) se configura el ámbito con `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-182">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="06ed3-183">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-183">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="06ed3-184">Los marcadores de posición `{CUSTOM SCOPE 1}` y `{CUSTOM SCOPE 2}` del ejemplo anterior son ámbitos personalizados.</span><span class="sxs-lookup"><span data-stu-id="06ed3-184">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="06ed3-185">El método `IAccessTokenProvider.RequestToken` proporciona una sobrecarga que permite a una aplicación aprovisionar un token de acceso con un determinado conjunto de ámbitos.</span><span class="sxs-lookup"><span data-stu-id="06ed3-185">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="06ed3-186">En un componente Razor:</span><span class="sxs-lookup"><span data-stu-id="06ed3-186">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="06ed3-187">Los marcadores de posición `{CUSTOM SCOPE 1}` y `{CUSTOM SCOPE 2}` del ejemplo anterior son ámbitos personalizados.</span><span class="sxs-lookup"><span data-stu-id="06ed3-187">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="06ed3-188"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> devuelve lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-188"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="06ed3-189">`true` con el `token` para su uso.</span><span class="sxs-lookup"><span data-stu-id="06ed3-189">`true` with the `token` for use.</span></span>
* <span data-ttu-id="06ed3-190">`false` si el token no se obtiene.</span><span class="sxs-lookup"><span data-stu-id="06ed3-190">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="06ed3-191">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="06ed3-191">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="06ed3-192">Al enviar credenciales (cookies de autorización o encabezados) en solicitudes de CORS, la directiva de CORS debe permitir el encabezado `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-192">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="06ed3-193">En la directiva siguiente se incluye la configuración de:</span><span class="sxs-lookup"><span data-stu-id="06ed3-193">The following policy includes configuration for:</span></span>

* <span data-ttu-id="06ed3-194">Orígenes de la solicitud (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-194">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="06ed3-195">Cualquier método (verbo).</span><span class="sxs-lookup"><span data-stu-id="06ed3-195">Any method (verb).</span></span>
* <span data-ttu-id="06ed3-196">Los encabezados `Content-Type` y `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-196">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="06ed3-197">Para permitir un encabezado personalizado (por ejemplo, `x-custom-header`), enumere el encabezado al llamar a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-197">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="06ed3-198">Las credenciales establecidas por el código JavaScript del lado cliente (la propiedad `credentials` establecida en `include`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-198">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="06ed3-199">Una solución hospedada Blazor basada en la plantilla del proyecto hospedada Blazor utiliza la misma dirección base para las aplicaciones cliente y servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-199">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="06ed3-200">De forma predeterminada, el elemento <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> de la aplicación cliente está establecido en un URI de `builder.HostEnvironment.BaseAddress`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-200">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="06ed3-201">La configuración de CORS **no** es necesaria en la configuración predeterminada de una aplicación hospedada creada a partir de la plantilla del proyecto hospedada de Blazor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-201">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="06ed3-202">Las aplicaciones cliente adicionales que no se hospedan en el proyecto de servidor y no comparten la dirección base de la aplicación de servidor **no** requieren la configuración de CORS en el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-202">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="06ed3-203">Para obtener más información, vea <xref:security/cors> y el componente del evaluador de solicitudes HTTP de la aplicación de ejemplo (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-203">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="06ed3-204">Controlar los errores de solicitudes de token</span><span class="sxs-lookup"><span data-stu-id="06ed3-204">Handle token request errors</span></span>

<span data-ttu-id="06ed3-205">Cuando una aplicación de página única autentica a un usuario a través de OpenID Connect (OIDC), el estado de autenticación se conserva localmente en dicha aplicación de página única y también en el proveedor de Identity en forma de cookie de sesión que se establece como consecuencia de que un usuario haya especificado sus credenciales.</span><span class="sxs-lookup"><span data-stu-id="06ed3-205">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="06ed3-206">Normalmente, los tokens que el proveedor de identidades emite para el usuario son válidos durante un breve período de tiempo (aproximadamente una hora), por lo que la aplicación cliente debe capturar nuevos tokens de manera regular.</span><span class="sxs-lookup"><span data-stu-id="06ed3-206">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="06ed3-207">De lo contrario, se cerrará la sesión del usuario después de que los tokens concedidos expiren.</span><span class="sxs-lookup"><span data-stu-id="06ed3-207">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="06ed3-208">En la mayoría de los casos, los clientes de OIDC pueden aprovisionar nuevos tokens sin necesidad de que el usuario tenga que autenticarse otra vez, gracias al estado de autenticación o a la "sesión" que el proveedor de identidades conserva.</span><span class="sxs-lookup"><span data-stu-id="06ed3-208">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="06ed3-209">Hay algunos casos en los que el cliente no puede obtener un token sin interacción del usuario, por ejemplo, cuando por alguna razón el usuario cierra la sesión del proveedor de identidades expresamente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-209">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="06ed3-210">Este escenario sucede si un usuario visita `https://login.microsoftonline.com` y cierra sesión. En estos casos, la aplicación no sabe de inmediato que el usuario ha cerrado la sesión. Es posible que el token que el cliente contiene ya no sea válido.</span><span class="sxs-lookup"><span data-stu-id="06ed3-210">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="06ed3-211">Además, el cliente no puede aprovisionar un token nuevo sin interacción del usuario después de que el token actual expire.</span><span class="sxs-lookup"><span data-stu-id="06ed3-211">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="06ed3-212">Estos escenarios no son específicos de la autenticación basada en tokens,</span><span class="sxs-lookup"><span data-stu-id="06ed3-212">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="06ed3-213">sino que forman parte de la idiosincrasia de las aplicaciones de página única.</span><span class="sxs-lookup"><span data-stu-id="06ed3-213">They are part of the nature of SPAs.</span></span> <span data-ttu-id="06ed3-214">Una aplicación de página única que use cookies tampoco podrá llamar a una API de servidor si la cookie de autenticación se quita.</span><span class="sxs-lookup"><span data-stu-id="06ed3-214">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="06ed3-215">Cuando una aplicación realiza llamadas API a recursos protegidos, hay que tener en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-215">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="06ed3-216">Para aprovisionar un nuevo token de acceso para llamar a la API, puede que el usuario deba autenticarse de nuevo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-216">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="06ed3-217">Aun cuando el cliente tenga un token que parezca válido, puede que se produzca un error en la llamada al servidor porque el usuario ha revocado dicho token.</span><span class="sxs-lookup"><span data-stu-id="06ed3-217">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="06ed3-218">Cuando la aplicación solicita un token, hay dos resultados posibles:</span><span class="sxs-lookup"><span data-stu-id="06ed3-218">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="06ed3-219">La solicitud se realiza correctamente y la aplicación tiene un token válido.</span><span class="sxs-lookup"><span data-stu-id="06ed3-219">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="06ed3-220">La solicitud no se realiza correctamente y la aplicación debe volver a autenticar al usuario para obtener un nuevo token.</span><span class="sxs-lookup"><span data-stu-id="06ed3-220">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="06ed3-221">Cuando una solicitud de token no se realiza correctamente, debe decidir si quiere guardar el estado actual antes de realizar una redirección.</span><span class="sxs-lookup"><span data-stu-id="06ed3-221">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="06ed3-222">Para ello, existen varios métodos con niveles de complejidad crecientes:</span><span class="sxs-lookup"><span data-stu-id="06ed3-222">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="06ed3-223">Almacenar el estado de la página actual en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-223">Store the current page state in session storage.</span></span> <span data-ttu-id="06ed3-224">Durante el [evento de ciclo de vida `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), compruebe si el estado se puede restaurar antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="06ed3-224">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="06ed3-225">Agregar un parámetro de cadena de consulta y usarlo como una manera de señalar la aplicación que necesita para volver a hidratar el estado guardado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-225">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="06ed3-226">Agregue un parámetro de cadena de consulta con un identificador único para almacenar los datos en el almacenamiento de sesión, sin riesgo de que se produzcan discrepancias con otros elementos.</span><span class="sxs-lookup"><span data-stu-id="06ed3-226">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="06ed3-227">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="06ed3-227">The following example shows how to:</span></span>

* <span data-ttu-id="06ed3-228">Conservar el estado antes de redirigir a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-228">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="06ed3-229">Recuperar el estado anterior tras la autenticación usando el parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="06ed3-229">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="06ed3-230">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="06ed3-230">Save app state before an authentication operation</span></span>

<span data-ttu-id="06ed3-231">Durante una operación de autenticación, hay casos en los que conviene guardar el estado de la aplicación antes de que el explorador se redirija a la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="06ed3-231">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="06ed3-232">Esto puede suceder cuando se usa un contenedor de estado y se quiere restaurar el estado después de que la autenticación se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-232">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="06ed3-233">Se puede usar un objeto de estado de autenticación personalizado para conservar el estado específico de la aplicación o una referencia a este, y restaurar el estado después de que la operación de autenticación se complete correctamente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-233">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="06ed3-234">Este método se describe en el siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-234">The following example demonstrates the approach.</span></span>

<span data-ttu-id="06ed3-235">Se crea una clase de contenedor de estado en la aplicación con propiedades que contienen los valores de estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-235">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="06ed3-236">En el siguiente ejemplo, el contenedor se usa para mantener el valor de contador del componente `Counter` de la plantilla del proyecto predeterminada (`Pages/Counter.razor`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-236">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="06ed3-237">Los métodos para serializar y deserializar el contenedor se basan en <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-237">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="06ed3-238">El componente `Counter` usa el contenedor de estado para conservar el valor de `currentCount` fuera del componente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-238">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="06ed3-239">Cree un objeto `ApplicationAuthenticationState` a partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-239">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="06ed3-240">Proporcione una propiedad `Id`, que actúa como identificador del estado almacenado localmente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-240">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="06ed3-241">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-241">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="06ed3-242">El componente `Authentication` (`Pages/Authentication.razor`) guarda y restaura el estado de la aplicación usando el almacenamiento de sesión local con los métodos de serialización y deserialización de `StateContainer`, `GetStateForLocalStorage` y `SetStateFromLocalStorage`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-242">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="06ed3-243">En este ejemplo se usa Azure Active Directory (AAD) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-243">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="06ed3-244">En `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-244">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="06ed3-245">El objeto `ApplicationAuthenticationState` se configura como el tipo `RemoteAuthenticationState` de la Biblioteca de autenticación de Microsoft (MSAL).</span><span class="sxs-lookup"><span data-stu-id="06ed3-245">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="06ed3-246">El contenedor de estado se registra en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06ed3-246">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="06ed3-247">Personalizar rutas de aplicación</span><span class="sxs-lookup"><span data-stu-id="06ed3-247">Customize app routes</span></span>

<span data-ttu-id="06ed3-248">La biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) usa de forma predeterminada las rutas indicadas en la siguiente tabla para representar distintos estados de autenticación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-248">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="06ed3-249">Ruta</span><span class="sxs-lookup"><span data-stu-id="06ed3-249">Route</span></span>                            | <span data-ttu-id="06ed3-250">Propósito</span><span class="sxs-lookup"><span data-stu-id="06ed3-250">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="06ed3-251">Desencadena una operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-251">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="06ed3-252">Controla el resultado de cualquier operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-252">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="06ed3-253">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-253">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="06ed3-254">Desencadena una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-254">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="06ed3-255">Controla el resultado de una operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-255">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="06ed3-256">Muestra mensajes de error cuando, por algún motivo, se produce un error en la operación de cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="06ed3-256">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="06ed3-257">Indica que el usuario ha cerrado sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-257">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="06ed3-258">Desencadena una operación para editar el perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-258">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="06ed3-259">Desencadena una operación para registrar un usuario nuevo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-259">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="06ed3-260">Las rutas que se muestran en la tabla anterior se pueden configurar a través de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-260">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="06ed3-261">Al establecer las opciones para proporcionar rutas personalizadas, confirme que la aplicación tiene una ruta que controla cada ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="06ed3-261">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="06ed3-262">En el siguiente ejemplo, todas las rutas de acceso tienen el prefijo `/security`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-262">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="06ed3-263">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-263">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="06ed3-264">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-264">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="06ed3-265">Si el requisito precisa de rutas de acceso completamente diferentes, establezca las rutas tal y como se ha descrito anteriormente y represente la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> con un parámetro de acción explícito:</span><span class="sxs-lookup"><span data-stu-id="06ed3-265">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="06ed3-266">La interfaz de usuario se puede fragmentar en diferentes páginas si así decide hacerlo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-266">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="06ed3-267">Personalizar la interfaz de usuario de autenticación</span><span class="sxs-lookup"><span data-stu-id="06ed3-267">Customize the authentication user interface</span></span>

<span data-ttu-id="06ed3-268"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> incluye un conjunto predeterminado de fragmentos de la interfaz de usuario para cada estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-268"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="06ed3-269">Cada estado se puede personalizar pasando un objeto <xref:Microsoft.AspNetCore.Components.RenderFragment> personalizado.</span><span class="sxs-lookup"><span data-stu-id="06ed3-269">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="06ed3-270">Para personalizar el texto que aparece durante el proceso de inicio de sesión inicial, puede cambiar la vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> como se indica aquí.</span><span class="sxs-lookup"><span data-stu-id="06ed3-270">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="06ed3-271">Componente `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-271">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="06ed3-272">La vista <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tiene un fragmento que se puede usar por cada ruta de autenticación que se muestra en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="06ed3-272">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="06ed3-273">Ruta</span><span class="sxs-lookup"><span data-stu-id="06ed3-273">Route</span></span>                            | <span data-ttu-id="06ed3-274">Fragmento</span><span class="sxs-lookup"><span data-stu-id="06ed3-274">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="06ed3-275">Personalizar el usuario</span><span class="sxs-lookup"><span data-stu-id="06ed3-275">Customize the user</span></span>

<span data-ttu-id="06ed3-276">Los usuarios enlazados a la aplicación se pueden personalizar.</span><span class="sxs-lookup"><span data-stu-id="06ed3-276">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="06ed3-277">Personalización del usuario con una notificación de carga</span><span class="sxs-lookup"><span data-stu-id="06ed3-277">Customize the user with a payload claim</span></span>

<span data-ttu-id="06ed3-278">En el siguiente ejemplo, los usuarios autenticados de la aplicación reciben una notificación de `amr` por cada uno de los métodos de autenticación del usuario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-278">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="06ed3-279">La notificación de `amr` identifica cómo se autenticó el firmante del token en las [notificaciones de carga](/azure/active-directory/develop/access-tokens#the-amr-claim) de la plataforma de Identity de Microsoft v1.0.</span><span class="sxs-lookup"><span data-stu-id="06ed3-279">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="06ed3-280">En el ejemplo se usa una clase de cuenta de usuario personalizada basada en <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-280">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="06ed3-281">Cree una clase que extienda la clase <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-281">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="06ed3-282">En el ejemplo siguiente se establece la propiedad `AuthenticationMethod` en la matriz del usuario de valores de propiedad JSON de `amr`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-282">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="06ed3-283">El marco rellena `AuthenticationMethod` automáticamente cuando se autentica el usuario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-283">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="06ed3-284">Cree un generador que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> para crear notificaciones desde los métodos de autenticación del usuario almacenados en `CustomUserAccount.AuthenticationMethod`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-284">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

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

<span data-ttu-id="06ed3-285">Registre la fábrica `CustomAccountFactory` del proveedor de autenticación en uso.</span><span class="sxs-lookup"><span data-stu-id="06ed3-285">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="06ed3-286">Cualquiera de los siguientes registros es válido:</span><span class="sxs-lookup"><span data-stu-id="06ed3-286">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="06ed3-287"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="06ed3-287"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="06ed3-288"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="06ed3-288"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="06ed3-289"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="06ed3-289"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

### <a name="customize-the-user-with-graph-api-claims"></a><span data-ttu-id="06ed3-290">Personalización del usuario con notificaciones de Graph API</span><span class="sxs-lookup"><span data-stu-id="06ed3-290">Customize the user with Graph API claims</span></span>

<span data-ttu-id="06ed3-291">En el ejemplo siguiente, la aplicación crea una notificación de número de teléfono móvil para el usuario a partir de Graph API mediante el elemento <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-291">In the following example, the app creates a mobile phone number claim for the user from Graph API using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span> <span data-ttu-id="06ed3-292">La aplicación debe tener el permiso de Graph API de `User.Read` (ámbito) configurado en AAD.</span><span class="sxs-lookup"><span data-stu-id="06ed3-292">The app must have the `User.Read` Graph API permission (scope) configured in AAD.</span></span>

<span data-ttu-id="06ed3-293">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-293">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="06ed3-294">Se crea un <xref:System.Net.Http.HttpClient> con nombre para Graph API en `Program.Main` (`Program.cs`) mediante el `GraphAPIAuthorizationMessageHandler`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-294">A named <xref:System.Net.Http.HttpClient> for Graph API is created in `Program.Main` (`Program.cs`) using the `GraphAPIAuthorizationMessageHandler`:</span></span>

```csharp
using System;

...

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="06ed3-295">`Models/UserInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="06ed3-295">`Models/UserInfo.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="06ed3-296">En el siguiente `CustomAccountFactory` (`CustomAccountFactory.cs`), el <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> del marco representa la cuenta del usuario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-296">In the following `CustomAccountFactory` (`CustomAccountFactory.cs`), the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="06ed3-297">Si la aplicación requiere una clase de cuenta de usuario personalizada que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-297">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {MESSAGE}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="06ed3-298">En `Program.Main` (`Program.cs`), configure la aplicación para usar el generador personalizado.</span><span class="sxs-lookup"><span data-stu-id="06ed3-298">In `Program.Main` (`Program.cs`), configure the app to use the custom factory.</span></span> <span data-ttu-id="06ed3-299">Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-299">If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="06ed3-300">El ejemplo anterior corresponde a una aplicación que usa autenticación de AAD con MSAL.</span><span class="sxs-lookup"><span data-stu-id="06ed3-300">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="06ed3-301">Existen patrones similares para la autenticación de API y OIDC.</span><span class="sxs-lookup"><span data-stu-id="06ed3-301">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="06ed3-302">Para más información, consulte los ejemplos al final de la sección [Personalización del usuario con una notificación de carga](#customize-the-user-with-a-payload-claim).</span><span class="sxs-lookup"><span data-stu-id="06ed3-302">For more information, see the examples at the end of the [Customize the user with a payload claim](#customize-the-user-with-a-payload-claim) section.</span></span>

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="06ed3-303">Grupos de seguridad y roles de AAD con una clase de cuenta de usuario personalizada</span><span class="sxs-lookup"><span data-stu-id="06ed3-303">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="06ed3-304">Para obtener un ejemplo adicional que funcione con los grupos de seguridad de AAD y los roles de administrador de AAD y una clase de cuenta de usuario personalizada, consulte <xref:blazor/security/webassembly/aad-groups-roles>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-304">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="06ed3-305">Compatibilidad de la representación previa con la autenticación</span><span class="sxs-lookup"><span data-stu-id="06ed3-305">Support prerendering with authentication</span></span>

<span data-ttu-id="06ed3-306">Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación hospedada Blazor WebAssembly, use estas instrucciones para crear una aplicación que:</span><span class="sxs-lookup"><span data-stu-id="06ed3-306">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="06ed3-307">Representa previamente las rutas de acceso para las que no se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="06ed3-307">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="06ed3-308">No representa previamente las rutas de acceso para las que se requiere autorización.</span><span class="sxs-lookup"><span data-stu-id="06ed3-308">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="06ed3-309">En la clase `Program` de la aplicación *`Client`* (`Program.cs`), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-309">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="06ed3-310">En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:</span><span class="sxs-lookup"><span data-stu-id="06ed3-310">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="06ed3-311">En el método `Startup.Configure` de la aplicación de servidor, reemplace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="06ed3-311">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="06ed3-312">En la aplicación de servidor, cree una carpeta `Pages`, si todavía no existe.</span><span class="sxs-lookup"><span data-stu-id="06ed3-312">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="06ed3-313">Cree una página `_Host.cshtml` dentro de la carpeta `Pages` de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-313">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="06ed3-314">Pegue el contenido del archivo `wwwroot/index.html` de la aplicación *`Client`* en el archivo `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-314">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="06ed3-315">Actualice el contenido del archivo:</span><span class="sxs-lookup"><span data-stu-id="06ed3-315">Update the file's contents:</span></span>

* <span data-ttu-id="06ed3-316">Agregue `@page "_Host"` a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="06ed3-316">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="06ed3-317">Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:</span><span class="sxs-lookup"><span data-stu-id="06ed3-317">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="06ed3-318">Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros</span><span class="sxs-lookup"><span data-stu-id="06ed3-318">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="06ed3-319">Al autenticar y autorizar una aplicación Blazor WebAssembly con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-319">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="06ed3-320">Su elección depende del escenario.</span><span class="sxs-lookup"><span data-stu-id="06ed3-320">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="06ed3-321">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-321">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="06ed3-322">Autenticación de usuarios solo para llamadas a las API de terceros protegidas</span><span class="sxs-lookup"><span data-stu-id="06ed3-322">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="06ed3-323">Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:</span><span class="sxs-lookup"><span data-stu-id="06ed3-323">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="06ed3-324">En este escenario:</span><span class="sxs-lookup"><span data-stu-id="06ed3-324">In this scenario:</span></span>

* <span data-ttu-id="06ed3-325">El servidor que hospeda la aplicación no desempeña ningún rol.</span><span class="sxs-lookup"><span data-stu-id="06ed3-325">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="06ed3-326">No se pueden proteger las API en el servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-326">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="06ed3-327">La aplicación solo puede llamar a las API de terceros protegidas.</span><span class="sxs-lookup"><span data-stu-id="06ed3-327">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="06ed3-328">Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero</span><span class="sxs-lookup"><span data-stu-id="06ed3-328">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="06ed3-329">Configure Identity con un proveedor de inicio de sesión de terceros.</span><span class="sxs-lookup"><span data-stu-id="06ed3-329">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="06ed3-330">Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.</span><span class="sxs-lookup"><span data-stu-id="06ed3-330">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="06ed3-331">Cuando un usuario inicia sesión, Identity recopila los tokens de acceso y actualización como parte del proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-331">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="06ed3-332">En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.</span><span class="sxs-lookup"><span data-stu-id="06ed3-332">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="06ed3-333">Uso de un token de acceso de servidor para recuperar el token de acceso de terceros</span><span class="sxs-lookup"><span data-stu-id="06ed3-333">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="06ed3-334">Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-334">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="06ed3-335">A partir de ahí, use el token de acceso de terceros para llamar directamente a los recursos de la API de terceros desde Identity en el cliente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-335">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="06ed3-336">Este enfoque no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="06ed3-336">We don't recommend this approach.</span></span> <span data-ttu-id="06ed3-337">Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público.</span><span class="sxs-lookup"><span data-stu-id="06ed3-337">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="06ed3-338">En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="06ed3-338">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="06ed3-339">Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.</span><span class="sxs-lookup"><span data-stu-id="06ed3-339">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="06ed3-340">El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.</span><span class="sxs-lookup"><span data-stu-id="06ed3-340">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="06ed3-341">Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.</span><span class="sxs-lookup"><span data-stu-id="06ed3-341">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="06ed3-342">Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros</span><span class="sxs-lookup"><span data-stu-id="06ed3-342">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="06ed3-343">Realice una llamada API desde el cliente a la API del servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-343">Make an API call from the client to the server API.</span></span> <span data-ttu-id="06ed3-344">En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="06ed3-344">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="06ed3-345">Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:</span><span class="sxs-lookup"><span data-stu-id="06ed3-345">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="06ed3-346">El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.</span><span class="sxs-lookup"><span data-stu-id="06ed3-346">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="06ed3-347">La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.</span><span class="sxs-lookup"><span data-stu-id="06ed3-347">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="06ed3-348">Uso de puntos de conexión de OpenID Connect (OIDC) v2.0</span><span class="sxs-lookup"><span data-stu-id="06ed3-348">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="06ed3-349">La biblioteca de autenticación y las plantillas de proyecto Blazor usan puntos de conexión de OpenID Connect (OIDC) v1.0.</span><span class="sxs-lookup"><span data-stu-id="06ed3-349">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="06ed3-350">Para usar un punto de conexión de la versión 2.0, configure la opción <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> del portador de JWT.</span><span class="sxs-lookup"><span data-stu-id="06ed3-350">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="06ed3-351">En el siguiente ejemplo, AAD se ha configurado para la versión 2.0 anexando un segmento `v2.0` a la propiedad <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="06ed3-351">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="06ed3-352">Como alternativa, esta opción se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-352">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="06ed3-353">Si la anexión de un segmento a la propiedad Authority no es adecuado para el proveedor de OIDC de la aplicación, como es el caso de los proveedores que no son AAD, establezca la propiedad <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> directamente.</span><span class="sxs-lookup"><span data-stu-id="06ed3-353">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="06ed3-354">Establezca la propiedad en <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o en el archivo de configuración de la aplicación (`appsettings.json`) con la clave `Authority`.</span><span class="sxs-lookup"><span data-stu-id="06ed3-354">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="06ed3-355">La lista de notificaciones en el token de identificador cambia en el caso de los puntos de conexión de la versión 2.0.</span><span class="sxs-lookup"><span data-stu-id="06ed3-355">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="06ed3-356">Para más información, vea [Motivos para actualizar a la Plataforma de identidad de Microsoft (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="06ed3-356">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="06ed3-357">Configuración y uso de gRPC en componentes</span><span class="sxs-lookup"><span data-stu-id="06ed3-357">Configure and use gRPC in components</span></span>

<span data-ttu-id="06ed3-358">Para configurar una aplicación Blazor WebAssembly que use el [marco gRPC de ASP.NET Core](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="06ed3-358">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="06ed3-359">Habilite gRPC-Web en el servidor.</span><span class="sxs-lookup"><span data-stu-id="06ed3-359">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="06ed3-360">Para obtener más información, vea <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-360">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="06ed3-361">Registre los servicios de gRPC para el controlador de mensajes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06ed3-361">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="06ed3-362">En el ejemplo siguiente se configura el controlador de mensajes de autorización de la aplicación para usar el servicio [`GreeterClient` del tutorial de gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-362">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="06ed3-363">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-363">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="06ed3-364">Coloque el archivo de `.proto` en el proyecto `Shared` de la solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="06ed3-364">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="06ed3-365">Un componente de la aplicación cliente puede realizar llamadas a gRPC mediante el cliente de gRPC (`Pages/Grpc.razor`):</span><span class="sxs-lookup"><span data-stu-id="06ed3-365">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="06ed3-366">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="06ed3-366">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="06ed3-367">Para usar la propiedad `Status.DebugException`, use [GRPC.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), versión 2.30.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="06ed3-367">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="06ed3-368">Para obtener más información, vea <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="06ed3-368">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06ed3-369">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="06ed3-369">Additional resources</span></span>

* [<span data-ttu-id="06ed3-370">`HttpClient` y `HttpRequestMessage` con las opciones de solicitud de la API de captura</span><span class="sxs-lookup"><span data-stu-id="06ed3-370">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
