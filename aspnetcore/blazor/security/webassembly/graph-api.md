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
ms.openlocfilehash: 569a88630f7b75e866d8ecda99605ebe3bc58db8
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234439"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Uso de Graph API con Blazor WebAssembly de ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api) es una API web RESTful que permite a Blazor y otras aplicaciones .NET Framework acceder a recursos del servicio en la nube de Microsoft.

## <a name="graph-sdk"></a>SDK de Graph

Los [SDK de Microsoft Graph](/graph/sdks/sdks-overview) se han diseñado para simplificar la compilación de aplicaciones eficaces, resistentes y de alta calidad que acceden a Microsoft Graph.

En los ejemplos de esta sección se necesitan referencias de paquete de los siguientes paquetes en el archivo del proyecto del archivo del proyecto de la aplicación independiente o *`Client`* :

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

En cada una de las siguientes subsecciones de este artículo se usan las siguientes clases de utilidad y configuración:

* [Llamada a Graph API desde un componente mediante el SDK de Graph](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Personalización de notificaciones de usuario con el SDK de Graph](#customize-user-claims-with-the-graph-sdk)

Después de agregar los ámbitos de Microsoft Graph API al área de AAD de Azure Portal:

* Agregue la siguiente clase `GraphClientExtensions.cs` a la aplicación independiente o la aplicación *`Client`* de una solución de Blazor hospedada.
* Proporcione los ámbitos necesarios para la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> en el método `AuthenticateRequestAsync`. En el siguiente ejemplo se especifica el ámbito `User.Read` para ajustarse a los ejemplos de secciones posteriores de este artículo.

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
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
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

El marcador de posición `{STRING ARRAY OF SCOPES}` del código anterior es una matriz de cadenas de los ámbitos permitidos. Por ejemplo, establezca `Scopes` en el ámbito `User.Read` en los ejemplos de las secciones siguientes de este artículo:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

En `Program.Main` (`Program.cs`), agregue los servicios y la configuración del cliente de Graph con el método de extensión `AddGraphClient`:

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

El marcador de posición `{STRING ARRAY OF SCOPES}` del código anterior es una matriz de cadenas de los ámbitos permitidos. Por ejemplo, pase el ámbito `User.Read` a `AddGraphClient` en los ejemplos de las secciones siguientes de este artículo:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Llamada a Graph API desde un componente mediante el SDK de Graph

En esta sección se usan las [clases de utilidad (`GraphClientExtensions.cs`)](#graph-sdk) descritas anteriormente en este artículo. El siguiente componente `GraphExample` usa un `GraphServiceClient` insertado para obtener los datos de perfil de AAD del usuario y mostrar su número de teléfono móvil:

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

### <a name="customize-user-claims-with-the-graph-sdk"></a>Personalización de notificaciones de usuario con el SDK de Graph

En esta sección se usan las [clases de utilidad (`GraphClientExtensions.cs`)](#graph-sdk) descritas anteriormente en este artículo.

En el ejemplo siguiente la aplicación crea una notificación de número de teléfono móvil de un usuario a partir del número de teléfono móvil de su perfil de usuario de AAD. La aplicación debe tener el ámbito de Graph API `User.Read` configurado en AAD.

En la siguiente fábrica de cuentas de usuario personalizada, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> del marco representa la cuenta del usuario. Si la aplicación requiere una clase de cuenta de usuario personalizada que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente.

`CustomAccountFactory.cs`:

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

En `Program.Main` (`Program.cs`), configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada: Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:

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

## <a name="named-client-with-graph-api"></a>Cliente con nombre con Graph API

En los ejemplos de esta sección se usa una instancia de <xref:System.Net.Http.HttpClient> con nombre para que Graph API obtenga el número de teléfono móvil de un usuario para procesar una llamada.

En los ejemplos de esta sección se necesita una referencia de paquete de [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) en el archivo del proyecto del archivo del proyecto de la aplicación independiente o *`Client`* .

Cree la siguiente configuración de clase y proyecto para trabajar con Graph API. En cada una de las siguientes subsecciones de este artículo se usan las siguientes clase y configuración:

* [Llamada a Graph API desde un componente](#call-graph-api-from-a-component)
* [Personalización de notificaciones de usuario con Graph API y un cliente con nombre](#customize-user-claims-with-graph-api-and-a-named-client)

Después de agregar los ámbitos de Microsoft Graph API al área de AAD de Azure Portal, proporcione los ámbitos necesarios para el controlador configurado de la aplicación para Graph API. En el ejemplo siguiente se configura el controlador para el ámbito `User.Read`. Se pueden agregar más ámbitos.

`GraphAuthorizationMessageHandler.cs`:

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

En `Program.Main` (`Program.cs`), configure la instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Llamada a Graph API desde un componente

En esta sección se usan el [controlador de mensajes de autorización de Graph (`GraphAuthorizationMessageHandler.cs`) y las adiciones `Program.Main` a la aplicación](#named-client-with-graph-api) que se han descrito anteriormente en este artículo, lo que proporciona una instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API.

En un componente Razor:

* Cree un <xref:System.Net.Http.HttpClient> para Graph API y emita una solicitud para los datos de perfil del usuario.
* La clase `UserInfo.cs` designa las propiedades de perfil de usuario necesarias con el atributo <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> y el nombre JSON que AAD usa para esas propiedades.

`Pages/CallUser.razor`:

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
> En el ejemplo anterior, el desarrollador implementa el `ICallProcessor` personalizado (`CallProcessor`) en la cola y luego realiza llamadas automatizadas.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Personalización de notificaciones de usuario con Graph API y un cliente con nombre

En esta sección se usan el [controlador de mensajes de autorización de Graph (`GraphAuthorizationMessageHandler.cs`) y las adiciones `Program.Main` a la aplicación](#named-client-with-graph-api) que se han descrito anteriormente en este artículo, lo que proporciona una instancia de <xref:System.Net.Http.HttpClient> con nombre para Graph API.

En el ejemplo siguiente la aplicación crea una notificación de número de teléfono móvil del usuario a partir del número de teléfono móvil de su perfil de usuario de AAD. La aplicación debe tener el ámbito de Graph API `User.Read` configurado en AAD.

Agregue una clase `UserInfo.cs` a la aplicación y designe las propiedades de perfil de usuario necesarias con el atributo <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> y el nombre JSON que usa AAD para esas propiedades:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

En la siguiente fábrica de cuentas de usuario personalizada, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> del marco representa la cuenta del usuario. Si la aplicación requiere una clase de cuenta de usuario personalizada que extienda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente.

`CustomAccountFactory.cs`:

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

En `Program.Main` (`Program.cs`), configure la autenticación de MSAL para usar la fábrica de cuentas de usuario personalizada: Si la aplicación usa una clase de cuenta de usuario personalizada que extiende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, intercambie la clase de cuenta de usuario personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> en el código siguiente:

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

El ejemplo anterior corresponde a una aplicación que usa autenticación de AAD con MSAL. Existen patrones similares para la autenticación de API y OIDC. Para obtener más información, vea los ejemplos de la sección [Personalización del usuario con una notificación de carga](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).
