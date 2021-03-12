---
title: Uso de Graph API con Blazor WebAssembly de ASP.NET Core
author: guardrex
description: Aprenda a usar Graph API con aplicaciones de WebAssembly de Blazor.
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: b9e95afc2ccff95c6acd215a70c8b0f92fb1b8c0
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394738"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="40ac9-103">Uso de Graph API con Blazor WebAssembly de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40ac9-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="40ac9-104">[Microsoft Graph API](/graph/use-the-api) es una API web RESTful que permite a Blazor y otras aplicaciones .NET Framework acceder a recursos del servicio en la nube de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="40ac9-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="40ac9-105">SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="40ac9-105">Graph SDK</span></span>

<span data-ttu-id="40ac9-106">Los [SDK de Microsoft Graph](/graph/sdks/sdks-overview) se han diseñado para simplificar la compilación de aplicaciones eficaces, resistentes y de alta calidad que acceden a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="40ac9-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="40ac9-107">En los ejemplos de esta sección se necesitan referencias de paquete de los siguientes paquetes en el archivo del proyecto del archivo del proyecto de la aplicación independiente o **`Client`** :</span><span class="sxs-lookup"><span data-stu-id="40ac9-107">The examples in this section require package references for the following packages in the project file of the standalone or **`Client`** app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="40ac9-108">En cada una de las siguientes subsecciones de este artículo se usan las siguientes clases de utilidad y configuración:</span><span class="sxs-lookup"><span data-stu-id="40ac9-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="40ac9-109">Llamada a Graph API desde un componente mediante el SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="40ac9-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="40ac9-110">Personalización de notificaciones de usuario con el SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="40ac9-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="40ac9-111">Después de agregar los ámbitos de Microsoft Graph API al área de AAD de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="40ac9-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="40ac9-112">Agregue la siguiente clase `GraphClientExtensions.cs` a la aplicación independiente o la aplicación **`Client`** de una solución de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="40ac9-112">Add the following `GraphClientExtensions.cs` class to the standalone app or **`Client`** app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="40ac9-113">Proporcione los ámbitos necesarios para la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> en el método `AuthenticateRequestAsync`.</span><span class="sxs-lookup"><span data-stu-id="40ac9-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="40ac9-114">En el siguiente ejemplo se especifica el ámbito `User.Read` para ajustarse a los ejemplos de secciones posteriores de este artículo.</span><span class="sxs-lookup"><span data-stu-id="40ac9-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="40ac9-115">Los marcadores de posición de ámbito `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` del código anterior representan uno o varios ámbitos permitidos.</span><span class="sxs-lookup"><span data-stu-id="40ac9-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="40ac9-116">Por ejemplo, establezca `Scopes` en una matriz de cadena de un ámbito para `User.Read` en los ejemplos de las secciones siguientes de este artículo:</span><span class="sxs-lookup"><span data-stu-id="40ac9-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="40ac9-117">En `Program.Main` (`Program.cs`), agregue los servicios y la configuración del cliente de Graph con el método de extensión `AddGraphClient`:</span><span class="sxs-lookup"><span data-stu-id="40ac9-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="40ac9-118">Los marcadores de posición de ámbito `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` del código anterior representan uno o varios ámbitos permitidos.</span><span class="sxs-lookup"><span data-stu-id="40ac9-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="40ac9-119">Por ejemplo, pase el ámbito `User.Read` a `AddGraphClient` en los ejemplos de las secciones siguientes de este artículo:</span><span class="sxs-lookup"><span data-stu-id="40ac9-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="40ac9-120">Llamada a Graph API desde un componente mediante el SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="40ac9-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="40ac9-121">En esta sección se usan las [clases de utilidad (`GraphClientExtensions.cs`)](#graph-sdk) descritas anteriormente en este artículo.</span><span class="sxs-lookup"><span data-stu-id="40ac9-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="40ac9-122">El siguiente componente `GraphExample` usa un `GraphServiceClient` insertado para obtener los datos de perfil de AAD del usuario y mostrar su número de teléfono móvil:</span><span class="sxs-lookup"><span data-stu-id="40ac9-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="40ac9-123">Personalización de notificaciones de usuario con el SDK de Graph</span><span class="sxs-lookup"><span data-stu-id="40ac9-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="40ac9-124">En esta sección se usan las [clases de utilidad (`GraphClientExtensions.cs`)](#graph-sdk) descritas anteriormente en este artículo.</span><span class="sxs-lookup"><span data-stu-id="40ac9-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="40ac9-125">En el ejemplo siguiente la aplicación crea una notificación de número de teléfono móvil de un usuario a partir del número de teléfono móvil de su perfil de usuario de AAD.</span><span class="sxs-lookup"><span data-stu-id="40ac9-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="40ac9-126">La aplicación debe tener el ámbito de Graph API `User.Read` configurado en AAD.</span><span class="sxs-lookup"><span data-stu-id="40ac9-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="40ac9-127">En la siguiente fábrica de cuentas de usuario personalizada, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> del marco representa la cuenta del usuario.</span><span class="sxs-lookup"><span data-stu-id="40ac9-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="40ac9-128">Si la aplicación requiere una clase de cuenta de usuario personalizada que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="40ac9-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="40ac9-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="40ac9-129">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
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
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="40ac9-130">En `Program.Main` (`Program.cs`), configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada: Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="40ac9-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="40ac9-131">Cliente con nombre con Graph API</span><span class="sxs-lookup"><span data-stu-id="40ac9-131">Named client with Graph API</span></span>

<span data-ttu-id="40ac9-132">En los ejemplos de esta sección se usa una instancia de <xref:System.Net.Http.HttpClient> con nombre para que Graph API obtenga el número de teléfono móvil de un usuario para procesar una llamada.</span><span class="sxs-lookup"><span data-stu-id="40ac9-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="40ac9-133">En los ejemplos de esta sección se necesita una referencia de paquete de [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) en el archivo del proyecto del archivo del proyecto de la aplicación independiente o **`Client`** .</span><span class="sxs-lookup"><span data-stu-id="40ac9-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or **`Client`** app's project file.</span></span>

<span data-ttu-id="40ac9-134">Cree la siguiente configuración de clase y proyecto para trabajar con Graph API.</span><span class="sxs-lookup"><span data-stu-id="40ac9-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="40ac9-135">En cada una de las siguientes subsecciones de este artículo se usan las siguientes clase y configuración:</span><span class="sxs-lookup"><span data-stu-id="40ac9-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="40ac9-136">Llamada a Graph API desde un componente</span><span class="sxs-lookup"><span data-stu-id="40ac9-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="40ac9-137">Personalización de notificaciones de usuario con Graph API y un cliente con nombre</span><span class="sxs-lookup"><span data-stu-id="40ac9-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="40ac9-138">Después de agregar los ámbitos de Microsoft Graph API al área de AAD de Azure Portal, proporcione los ámbitos necesarios para el controlador configurado de la aplicación para Graph API.</span><span class="sxs-lookup"><span data-stu-id="40ac9-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="40ac9-139">En el ejemplo siguiente se configura el controlador para el ámbito `User.Read`.</span><span class="sxs-lookup"><span data-stu-id="40ac9-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="40ac9-140">Se pueden agregar más ámbitos.</span><span class="sxs-lookup"><span data-stu-id="40ac9-140">Additional scopes can be added.</span></span>

<span data-ttu-id="40ac9-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="40ac9-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="40ac9-142">En `Program.Main` (`Program.cs`), configure la instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API:</span><span class="sxs-lookup"><span data-stu-id="40ac9-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="40ac9-143">Llamada a Graph API desde un componente</span><span class="sxs-lookup"><span data-stu-id="40ac9-143">Call Graph API from a component</span></span>

<span data-ttu-id="40ac9-144">En esta sección se usan el [controlador de mensajes de autorización de Graph (`GraphAuthorizationMessageHandler.cs`) y las adiciones `Program.Main` a la aplicación](#named-client-with-graph-api) que se han descrito anteriormente en este artículo, lo que proporciona una instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API.</span><span class="sxs-lookup"><span data-stu-id="40ac9-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="40ac9-145">En un componente Razor:</span><span class="sxs-lookup"><span data-stu-id="40ac9-145">In a Razor component:</span></span>

* <span data-ttu-id="40ac9-146">Cree un <xref:System.Net.Http.HttpClient> para Graph API y emita una solicitud para los datos de perfil del usuario.</span><span class="sxs-lookup"><span data-stu-id="40ac9-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="40ac9-147">La clase `UserInfo.cs` designa las propiedades de perfil de usuario necesarias con el atributo <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> y el nombre JSON que AAD usa para esas propiedades.</span><span class="sxs-lookup"><span data-stu-id="40ac9-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="40ac9-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="40ac9-148">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

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
                // Use userInfo.MobilePhone and callInfo.Message to make a call

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

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="40ac9-149">Personalización de notificaciones de usuario con Graph API y un cliente con nombre</span><span class="sxs-lookup"><span data-stu-id="40ac9-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="40ac9-150">En esta sección se usan el [controlador de mensajes de autorización de Graph (`GraphAuthorizationMessageHandler.cs`) y las adiciones `Program.Main` a la aplicación](#named-client-with-graph-api) que se han descrito anteriormente en este artículo, lo que proporciona una instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API.</span><span class="sxs-lookup"><span data-stu-id="40ac9-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="40ac9-151">En el ejemplo siguiente la aplicación crea una notificación de número de teléfono móvil del usuario a partir del número de teléfono móvil de su perfil de usuario de AAD.</span><span class="sxs-lookup"><span data-stu-id="40ac9-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="40ac9-152">La aplicación debe tener el ámbito de Graph API `User.Read` configurado en AAD.</span><span class="sxs-lookup"><span data-stu-id="40ac9-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="40ac9-153">Agregue una clase `UserInfo.cs` a la aplicación y designe las propiedades de perfil de usuario necesarias con el atributo <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> y el nombre JSON que usa AAD para esas propiedades:</span><span class="sxs-lookup"><span data-stu-id="40ac9-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="40ac9-154">En la siguiente fábrica de cuentas de usuario personalizada, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> del marco representa la cuenta del usuario.</span><span class="sxs-lookup"><span data-stu-id="40ac9-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="40ac9-155">Si la aplicación requiere una clase de cuenta de usuario personalizada que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="40ac9-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="40ac9-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="40ac9-156">`CustomAccountFactory.cs`:</span></span>

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
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="40ac9-157">En `Program.Main` (`Program.cs`), configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada: Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="40ac9-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
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

<span data-ttu-id="40ac9-158">El ejemplo anterior corresponde a una aplicación que usa autenticación de AAD con MSAL.</span><span class="sxs-lookup"><span data-stu-id="40ac9-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="40ac9-159">Existen patrones similares para la autenticación de API y OIDC.</span><span class="sxs-lookup"><span data-stu-id="40ac9-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="40ac9-160">Para obtener más información, vea los ejemplos de la sección [Personalización del usuario con una notificación de carga](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).</span><span class="sxs-lookup"><span data-stu-id="40ac9-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
