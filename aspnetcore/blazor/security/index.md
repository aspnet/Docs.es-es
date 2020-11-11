---
title: 'Autenticación y autorización de ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Obtenga información sobre los escenarios de autenticación y autorización de :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/index
ms.openlocfilehash: a333c189e81a9f44e94deb6b37097f1a8b19a0f9
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430931"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="57c7a-103">Autenticación y autorización de ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="57c7a-103">ASP.NET Core :::no-loc(Blazor)::: authentication and authorization</span></span>

<span data-ttu-id="57c7a-104">De [Steve Sanderson](https://github.com/SteveSandersonMS) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="57c7a-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="57c7a-105">ASP.NET Core admite la configuración y administración de seguridad en las aplicaciones de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-105">ASP.NET Core supports the configuration and management of security in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="57c7a-106">Los escenarios de seguridad difieren entre las aplicaciones de :::no-loc(Blazor Server)::: y :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-106">Security scenarios differ between :::no-loc(Blazor Server)::: and :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="57c7a-107">Debido a que las aplicaciones de :::no-loc(Blazor Server)::: se ejecutan en el servidor, las comprobaciones de autorización pueden determinar:</span><span class="sxs-lookup"><span data-stu-id="57c7a-107">Because :::no-loc(Blazor Server)::: apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="57c7a-108">Las opciones de la interfaz de usuario presentadas a un usuario (por ejemplo, qué entradas de menú están disponibles para el usuario).</span><span class="sxs-lookup"><span data-stu-id="57c7a-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="57c7a-109">Las reglas de acceso para las áreas de la aplicación y los componentes.</span><span class="sxs-lookup"><span data-stu-id="57c7a-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="57c7a-110">Las aplicaciones de :::no-loc(Blazor WebAssembly)::: se ejecutan en el cliente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-110">:::no-loc(Blazor WebAssembly)::: apps run on the client.</span></span> <span data-ttu-id="57c7a-111">La autorización *solo* se utiliza para determinar qué opciones de la interfaz de usuario se van a mostrar.</span><span class="sxs-lookup"><span data-stu-id="57c7a-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="57c7a-112">Dado que el usuario puede modificar u omitir las comprobaciones en el cliente, las aplicaciones de :::no-loc(Blazor WebAssembly)::: no pueden aplicar reglas de acceso de autorización.</span><span class="sxs-lookup"><span data-stu-id="57c7a-112">Since client-side checks can be modified or bypassed by a user, a :::no-loc(Blazor WebAssembly)::: app can't enforce authorization access rules.</span></span>

<span data-ttu-id="57c7a-113">Las [convenciones de autorización de :::no-loc(Razor)::: Pages](xref:security/authorization/razor-pages-authorization) no se aplican a los componentes de :::no-loc(Razor)::: enrutables.</span><span class="sxs-lookup"><span data-stu-id="57c7a-113">[:::no-loc(Razor)::: Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable :::no-loc(Razor)::: components.</span></span> <span data-ttu-id="57c7a-114">Si [se inserta un componente no enrutable de :::no-loc(Razor)::: en una página](xref:blazor/components/prerendering-and-integration), las convenciones de autorización de esta afectan indirectamente al componente de :::no-loc(Razor)::: y al resto del contenido de la página.</span><span class="sxs-lookup"><span data-stu-id="57c7a-114">If a non-routable :::no-loc(Razor)::: component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the :::no-loc(Razor)::: component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="57c7a-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> y <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> no se admiten en los componentes de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> aren't supported in :::no-loc(Razor)::: components.</span></span>

## <a name="authentication"></a><span data-ttu-id="57c7a-116">Autenticación</span><span class="sxs-lookup"><span data-stu-id="57c7a-116">Authentication</span></span>

<span data-ttu-id="57c7a-117">:::no-loc(Blazor)::: usa los mecanismos de autenticación de ASP.NET Core existentes para establecer la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="57c7a-117">:::no-loc(Blazor)::: uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="57c7a-118">El mecanismo exacto depende de la forma en la que se hospeda la aplicación de :::no-loc(Blazor):::: :::no-loc(Blazor WebAssembly)::: o :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-118">The exact mechanism depends on how the :::no-loc(Blazor)::: app is hosted, :::no-loc(Blazor WebAssembly)::: or :::no-loc(Blazor Server):::.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="57c7a-119">autenticación :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="57c7a-119">:::no-loc(Blazor WebAssembly)::: authentication</span></span>

<span data-ttu-id="57c7a-120">En las aplicaciones de :::no-loc(Blazor WebAssembly):::, las comprobaciones de autenticación pueden omitirse porque los usuarios pueden modificar todos los códigos del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-120">In :::no-loc(Blazor WebAssembly)::: apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="57c7a-121">Lo mismo se aplica a todas las tecnologías de aplicaciones del lado cliente, incluidas las plataformas JavaScript SPA o las aplicaciones nativas para cualquier sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="57c7a-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="57c7a-122">Agregue la siguiente línea de código:</span><span class="sxs-lookup"><span data-stu-id="57c7a-122">Add the following:</span></span>

* <span data-ttu-id="57c7a-123">Una referencia de paquete para [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) al archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57c7a-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="57c7a-124">El espacio de nombres de `Microsoft.AspNetCore.Components.Authorization` al archivo `_Imports.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57c7a-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="57c7a-125">Para controlar la autenticación, el uso de un servicio <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> integrado o personalizado se describe en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="57c7a-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="57c7a-126">Para más información sobre cómo crear aplicaciones y su configuración, consulte <xref:blazor/security/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="57c7a-127">autenticación :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="57c7a-127">:::no-loc(Blazor Server)::: authentication</span></span>

<span data-ttu-id="57c7a-128">Las aplicaciones de :::no-loc(Blazor Server)::: funcionan mediante una conexión en tiempo real que se crea con :::no-loc(SignalR):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-128">:::no-loc(Blazor Server)::: apps operate over a real-time connection that's created using :::no-loc(SignalR):::.</span></span> <span data-ttu-id="57c7a-129">La [autenticación en aplicaciones basadas en :::no-loc(SignalR):::](xref:signalr/authn-and-authz) se controla cuando se establece la conexión.</span><span class="sxs-lookup"><span data-stu-id="57c7a-129">[Authentication in :::no-loc(SignalR):::-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="57c7a-130">La autenticación se puede basar en una :::no-loc(cookie)::: o en cualquier otro token de portador.</span><span class="sxs-lookup"><span data-stu-id="57c7a-130">Authentication can be based on a :::no-loc(cookie)::: or some other bearer token.</span></span>

<span data-ttu-id="57c7a-131">El servicio <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> integrado para aplicaciones :::no-loc(Blazor Server)::: obtiene datos de estado de autenticación de `HttpContext.User` de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57c7a-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for :::no-loc(Blazor Server)::: apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="57c7a-132">Así es como el estado de autenticación se integra con los mecanismos de autenticación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57c7a-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="57c7a-133">Para más información sobre cómo crear aplicaciones y su configuración, consulte <xref:blazor/security/server/index>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="57c7a-134">Servicio AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="57c7a-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="57c7a-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> es el servicio subyacente utilizado por el componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> y el componente <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> para obtener el estado de autenticación.</span><span class="sxs-lookup"><span data-stu-id="57c7a-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="57c7a-136">Por lo general, no se utiliza <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directamente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="57c7a-137">Use los enfoques del [componente `AuthorizeView`](#authorizeview-component) o [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) descritos más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="57c7a-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="57c7a-138">El principal inconveniente de utilizar <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directamente es que el componente no se notifica de manera automática si cambia el estado de autenticación subyacente de los datos.</span><span class="sxs-lookup"><span data-stu-id="57c7a-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="57c7a-139">El servicio <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> puede proporcionar los datos <xref:System.Security.Claims.ClaimsPrincipal> del usuario actual, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="57c7a-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="57c7a-140">Si `user.:::no-loc(Identity):::.IsAuthenticated` es `true` y porque el usuario es <xref:System.Security.Claims.ClaimsPrincipal>, se pueden enumerar las notificaciones y evaluar la pertenencia a roles.</span><span class="sxs-lookup"><span data-stu-id="57c7a-140">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="57c7a-141">Para más información sobre la inserción de dependencias (DI) y servicios, consulte <xref:blazor/fundamentals/dependency-injection> y <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="57c7a-142">Implementación de un componente AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="57c7a-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="57c7a-143">Si la aplicación requiere un proveedor personalizado, implemente <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> e invalide `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new Claims:::no-loc(Identity):::(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="57c7a-144">En una aplicación de :::no-loc(Blazor WebAssembly):::, el servicio `CustomAuthStateProvider` se registra en `Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-144">In a :::no-loc(Blazor WebAssembly)::: app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="57c7a-145">En una aplicación de :::no-loc(Blazor Server):::, el servicio `CustomAuthStateProvider` se registra en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-145">In a :::no-loc(Blazor Server)::: app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="57c7a-146">Con el `CustomAuthStateProvider` en el ejemplo anterior, todos los usuarios se autentican con el nombre de usuario `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="57c7a-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="57c7a-147">Exposición del estado de autenticación como un parámetro en cascada</span><span class="sxs-lookup"><span data-stu-id="57c7a-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="57c7a-148">Si se requieren los datos de estado de autenticación para la lógica de procedimiento, como cuando se realiza una acción desencadenada por el usuario, obtenga los datos de estado de autenticación mediante la definición de un parámetro en cascada del tipo `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="57c7a-149">Si `user.:::no-loc(Identity):::.IsAuthenticated` es `true`, se pueden enumerar las notificaciones y evaluar la pertenencia a roles.</span><span class="sxs-lookup"><span data-stu-id="57c7a-149">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="57c7a-150">Configure el parámetro en cascada `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` mediante los componentes <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> y <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="57c7a-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="57c7a-151">En una aplicación de :::no-loc(Blazor WebAssembly):::, agregue servicios relativos a opciones y autorización a `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-151">In a :::no-loc(Blazor WebAssembly)::: App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="57c7a-152">En una aplicación de :::no-loc(Blazor Server):::, ya existen servicios para opciones y autorización, por lo que no es necesario realizar ninguna otra acción.</span><span class="sxs-lookup"><span data-stu-id="57c7a-152">In a :::no-loc(Blazor Server)::: app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="57c7a-153">Autorización</span><span class="sxs-lookup"><span data-stu-id="57c7a-153">Authorization</span></span>

<span data-ttu-id="57c7a-154">Cuando un usuario está autenticado, se aplican las reglas de *autorización* para controlar qué puede hacer el usuario.</span><span class="sxs-lookup"><span data-stu-id="57c7a-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="57c7a-155">Por lo general, se concede o deniega el acceso en función de si:</span><span class="sxs-lookup"><span data-stu-id="57c7a-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="57c7a-156">El usuario está autenticado (ha iniciado sesión).</span><span class="sxs-lookup"><span data-stu-id="57c7a-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="57c7a-157">El usuario está en un *rol*.</span><span class="sxs-lookup"><span data-stu-id="57c7a-157">A user is in a *role*.</span></span>
* <span data-ttu-id="57c7a-158">El usuario tiene una *notificación*.</span><span class="sxs-lookup"><span data-stu-id="57c7a-158">A user has a *claim*.</span></span>
* <span data-ttu-id="57c7a-159">Una *directiva* se cumple.</span><span class="sxs-lookup"><span data-stu-id="57c7a-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="57c7a-160">Cada uno de estos conceptos tiene su equivalente en una aplicación ASP.NET Core MVC o :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="57c7a-160">Each of these concepts is the same as in an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app.</span></span> <span data-ttu-id="57c7a-161">Para más información sobre la seguridad de ASP.NET Core, consulte los artículos disponibles en [Seguridad e :::no-loc(Identity)::: de ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="57c7a-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and :::no-loc(Identity):::](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="57c7a-162">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="57c7a-162">AuthorizeView component</span></span>

<span data-ttu-id="57c7a-163">El componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> selectivamente muestra la interfaz de usuario dependiendo de si el usuario está autorizado para verlo.</span><span class="sxs-lookup"><span data-stu-id="57c7a-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="57c7a-164">Este enfoque es útil cuando solo necesite *mostrar* datos del usuario y no es necesario usar la identidad del usuario en la lógica de procedimientos.</span><span class="sxs-lookup"><span data-stu-id="57c7a-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="57c7a-165">El componente expone una variable `context` del tipo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, que puede utilizar para acceder a la información sobre el usuario que ha iniciado sesión:</span><span class="sxs-lookup"><span data-stu-id="57c7a-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="57c7a-166">También puede proporcionar contenido diferente para mostrar si el usuario no está autenticado:</span><span class="sxs-lookup"><span data-stu-id="57c7a-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="57c7a-167">El componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> se puede usar en el componente `NavMenu` (`Shared/NavMenu.razor`) para mostrar un elemento de lista (`<li>...</li>`) para un [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), pero tenga en cuenta que este enfoque solo quita el elemento de lista desde la salida representada.</span><span class="sxs-lookup"><span data-stu-id="57c7a-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="57c7a-168">No impide que el usuario navegue hasta el componente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="57c7a-169">El contenido de las etiquetas `<Authorized>` y `<NotAuthorized>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="57c7a-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="57c7a-170">Las condiciones de autorización, como los roles o directivas que controlan las opciones o el acceso a la interfaz de usuario, se tratan en la sección [Autorización](#authorization).</span><span class="sxs-lookup"><span data-stu-id="57c7a-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="57c7a-171">Si no se especifican las condiciones de la autorización, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa una directiva predeterminada y trata:</span><span class="sxs-lookup"><span data-stu-id="57c7a-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="57c7a-172">A los usuarios autenticados (con sesión iniciada) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="57c7a-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="57c7a-173">A los usuarios no autenticados (sin sesión no iniciada) como no autorizados.</span><span class="sxs-lookup"><span data-stu-id="57c7a-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="57c7a-174">Autorización basada en roles y en directivas</span><span class="sxs-lookup"><span data-stu-id="57c7a-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="57c7a-175">El componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> admite la autorización *basada en roles* o *basada en directivas*.</span><span class="sxs-lookup"><span data-stu-id="57c7a-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="57c7a-176">Para la autorización basada en roles, utilice el parámetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:</span><span class="sxs-lookup"><span data-stu-id="57c7a-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="57c7a-177">Para obtener más información, vea <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="57c7a-178">Para la autorización basada en directivas, utilice el parámetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:</span><span class="sxs-lookup"><span data-stu-id="57c7a-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="57c7a-179">La autorización basada en notificaciones es un caso especial de autorización basada en directivas.</span><span class="sxs-lookup"><span data-stu-id="57c7a-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="57c7a-180">Por ejemplo, puede definir una directiva que requiere que los usuarios tengan una notificación determinada.</span><span class="sxs-lookup"><span data-stu-id="57c7a-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="57c7a-181">Para obtener más información, vea <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="57c7a-182">Estas API se pueden usar en aplicaciones de :::no-loc(Blazor Server)::: o :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-182">These APIs can be used in either :::no-loc(Blazor Server)::: or :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="57c7a-183">Si no se especifica <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> ni <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="57c7a-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="57c7a-184">Contenido que se muestra durante la autenticación asincrónica</span><span class="sxs-lookup"><span data-stu-id="57c7a-184">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="57c7a-185">:::no-loc(Blazor)::: permite que el estado de autenticación se determine *asincrónicamente*.</span><span class="sxs-lookup"><span data-stu-id="57c7a-185">:::no-loc(Blazor)::: allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="57c7a-186">El escenario principal de este enfoque se encuentra en las aplicaciones de :::no-loc(Blazor WebAssembly)::: que realizan una solicitud de autenticación a un punto de conexión externo.</span><span class="sxs-lookup"><span data-stu-id="57c7a-186">The primary scenario for this approach is in :::no-loc(Blazor WebAssembly)::: apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="57c7a-187">Mientras la autenticación está en curso, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> no muestra ningún contenido de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="57c7a-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="57c7a-188">Para mostrar el contenido mientras se produce autenticación, use la etiqueta `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="57c7a-188">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="57c7a-189">Este enfoque no se aplica normalmente a las aplicaciones de :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-189">This approach isn't normally applicable to :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="57c7a-190">Las aplicaciones de :::no-loc(Blazor Server)::: conocen el estado de autenticación tan pronto como se establece dicho estado.</span><span class="sxs-lookup"><span data-stu-id="57c7a-190">:::no-loc(Blazor Server)::: apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="57c7a-191">El contenido <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> puede proporcionarse en un componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> de una aplicación de :::no-loc(Blazor Server):::, pero nunca se muestra.</span><span class="sxs-lookup"><span data-stu-id="57c7a-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a :::no-loc(Blazor Server)::: app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="57c7a-192">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="57c7a-192">[Authorize] attribute</span></span>

<span data-ttu-id="57c7a-193">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se puede usar en los componentes de :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="57c7a-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="57c7a-194">Utilice únicamente [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en componentes `@page` a los que se llega a través del enrutador de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="57c7a-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the :::no-loc(Blazor)::: Router.</span></span> <span data-ttu-id="57c7a-195">La autorización solo se realiza como un aspecto del enrutamiento y *no* para los componentes secundarios representados dentro de una página.</span><span class="sxs-lookup"><span data-stu-id="57c7a-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="57c7a-196">Para autorizar la presentación de partes concretas dentro de una página, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> en su lugar.</span><span class="sxs-lookup"><span data-stu-id="57c7a-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="57c7a-197">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) admite también la autorización basada en roles o basada en directivas.</span><span class="sxs-lookup"><span data-stu-id="57c7a-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="57c7a-198">Para la autorización basada en roles, utilice el parámetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:</span><span class="sxs-lookup"><span data-stu-id="57c7a-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="57c7a-199">Para la autorización basada en directivas, utilice el parámetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:</span><span class="sxs-lookup"><span data-stu-id="57c7a-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="57c7a-200">Si no se especifica <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> ni <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) usa la directiva predeterminada, que consiste en tratar:</span><span class="sxs-lookup"><span data-stu-id="57c7a-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="57c7a-201">A los usuarios autenticados (con sesión iniciada) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="57c7a-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="57c7a-202">A los usuarios no autenticados (sin sesión no iniciada) como no autorizados.</span><span class="sxs-lookup"><span data-stu-id="57c7a-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="57c7a-203">Personalización del contenido no autorizado con el componente de enrutador</span><span class="sxs-lookup"><span data-stu-id="57c7a-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="57c7a-204">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router>, junto con el componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>, permite que la aplicación especifique el contenido personalizado si:</span><span class="sxs-lookup"><span data-stu-id="57c7a-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="57c7a-205">No se encuentra el contenido.</span><span class="sxs-lookup"><span data-stu-id="57c7a-205">Content isn't found.</span></span>
* <span data-ttu-id="57c7a-206">El usuario produce un error en la condición [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicada al componente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="57c7a-207">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se describe en la sección [`[Authorize]`Atributo ](#authorize-attribute).</span><span class="sxs-lookup"><span data-stu-id="57c7a-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="57c7a-208">La autenticación asincrónica está en curso.</span><span class="sxs-lookup"><span data-stu-id="57c7a-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="57c7a-209">En la plantilla de proyecto de :::no-loc(Blazor Server)::: predeterminada, el componente `App` (`App.razor`) muestra cómo configurar el contenido personalizado:</span><span class="sxs-lookup"><span data-stu-id="57c7a-209">In the default :::no-loc(Blazor Server)::: project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="57c7a-210">El contenido de las etiquetas `<NotFound>`, `<NotAuthorized>` y `<Authorizing>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="57c7a-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="57c7a-211">Si no se especifica la etiqueta `<NotAuthorized>`, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> usa el siguiente mensaje de reserva:</span><span class="sxs-lookup"><span data-stu-id="57c7a-211">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="57c7a-212">Notificación sobre los cambios de estado de autenticación</span><span class="sxs-lookup"><span data-stu-id="57c7a-212">Notification about authentication state changes</span></span>

<span data-ttu-id="57c7a-213">Si la aplicación determina que los datos de estado de autenticación subyacentes han cambiado (por ejemplo, porque el usuario ha cerrado sesión o porque otro usuario ha cambiado sus roles), un [`AuthenticationStateProvider` personalizado](#implement-a-custom-authenticationstateprovider) puede opcionalmente invocar el método <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> en la clase base <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>.</span><span class="sxs-lookup"><span data-stu-id="57c7a-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="57c7a-214">Esto notifica a los consumidores de los datos de estado de autenticación (por ejemplo, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) para que los vuelvan a procesar utilizando los nuevos datos.</span><span class="sxs-lookup"><span data-stu-id="57c7a-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="57c7a-215">Lógica de procedimientos</span><span class="sxs-lookup"><span data-stu-id="57c7a-215">Procedural logic</span></span>

<span data-ttu-id="57c7a-216">Si se requiere que la aplicación compruebe las reglas de autorización como parte de la lógica de procedimiento, utilice un parámetro en cascada del tipo `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` para obtener el <xref:System.Security.Claims.ClaimsPrincipal> del usuario.</span><span class="sxs-lookup"><span data-stu-id="57c7a-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="57c7a-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` se puede combinar con otros servicios, como `IAuthorizationService`, para evaluar las directivas.</span><span class="sxs-lookup"><span data-stu-id="57c7a-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="57c7a-218">En un componente de la aplicación de :::no-loc(Blazor WebAssembly):::, agregue los espacios de nombres <xref:Microsoft.AspNetCore.Authorization> y <xref:Microsoft.AspNetCore.Components.Authorization>:</span><span class="sxs-lookup"><span data-stu-id="57c7a-218">In a :::no-loc(Blazor WebAssembly)::: app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="57c7a-219">Estos espacios de nombres se pueden proporcionar globalmente si se agregan al archivo `_Imports.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57c7a-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="57c7a-220">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="57c7a-220">Troubleshoot errors</span></span>

<span data-ttu-id="57c7a-221">Errores comunes:</span><span class="sxs-lookup"><span data-stu-id="57c7a-221">Common errors:</span></span>

* <span data-ttu-id="57c7a-222">**La autorización requiere un parámetro en cascada de tipo `Task\<AuthenticationState>`. Considere la posibilidad de usar `CascadingAuthenticationState` para proporcionarla.**</span><span class="sxs-lookup"><span data-stu-id="57c7a-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="57c7a-223">**Se recibe el valor `null` para `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="57c7a-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="57c7a-224">Es probable que el proyecto no se haya creado mediante una plantilla de :::no-loc(Blazor Server)::: con la autenticación habilitada.</span><span class="sxs-lookup"><span data-stu-id="57c7a-224">It's likely that the project wasn't created using a :::no-loc(Blazor Server)::: template with authentication enabled.</span></span> <span data-ttu-id="57c7a-225">Encapsule un `<CascadingAuthenticationState>` alrededor de alguna parte del árbol de la interfaz de usuario, por ejemplo, en el componente `App` (`App.razor`) de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="57c7a-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="57c7a-226">El <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> proporciona el parámetro en cascada `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, que a su vez recibe el servicio DI <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> subyacente.</span><span class="sxs-lookup"><span data-stu-id="57c7a-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57c7a-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="57c7a-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="57c7a-228">[Genial:::no-loc(Blazor):::: Vínculos de ejemplo de la comunidad de autenticación](https://github.com/AdrienTorris/awesome-blazor#authentication)</span><span class="sxs-lookup"><span data-stu-id="57c7a-228">[Awesome :::no-loc(Blazor):::: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
