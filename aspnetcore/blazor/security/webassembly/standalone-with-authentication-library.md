---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: be87257c5f901e9b3d1ba6a8d7c6b811419c433f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402200"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="856c2-102">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="856c2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="856c2-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="856c2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="856c2-104">*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*</span><span class="sxs-lookup"><span data-stu-id="856c2-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="856c2-105">Para crear una aplicación independiente Blazor WebAssembly que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/), ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="856c2-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="856c2-106">Para especificar la ubicación de salida, lo que crea una carpeta de proyecto si no existe, incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="856c2-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="856c2-107">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="856c2-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="856c2-108">En Visual Studio, [cree una aplicación Blazor WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="856c2-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="856c2-109">Establezca **Autenticación** en **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación**.</span><span class="sxs-lookup"><span data-stu-id="856c2-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="856c2-110">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="856c2-110">Authentication package</span></span>

<span data-ttu-id="856c2-111">Cuando una aplicación se crea para usar cuentas de usuario individuales, dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="856c2-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="856c2-112">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="856c2-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="856c2-113">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="856c2-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="856c2-114">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="856c2-114">Authentication service support</span></span>

<span data-ttu-id="856c2-115">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> proporcionado por el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="856c2-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="856c2-116">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="856c2-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="856c2-117">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="856c2-117">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="856c2-118">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="856c2-118">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="856c2-119">La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="856c2-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="856c2-120">El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC.</span><span class="sxs-lookup"><span data-stu-id="856c2-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="856c2-121">Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="856c2-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="856c2-122">Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.</span><span class="sxs-lookup"><span data-stu-id="856c2-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="856c2-123">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="856c2-123">Access token scopes</span></span>

<span data-ttu-id="856c2-124">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="856c2-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="856c2-125">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="856c2-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="856c2-126">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="856c2-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="856c2-127">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="856c2-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="856c2-128">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="856c2-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="856c2-129">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="856c2-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="856c2-130">Página de índice</span><span class="sxs-lookup"><span data-stu-id="856c2-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="856c2-131">Componente App</span><span class="sxs-lookup"><span data-stu-id="856c2-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="856c2-132">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="856c2-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="856c2-133">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="856c2-133">LoginDisplay component</span></span>

<span data-ttu-id="856c2-134">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="856c2-134">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="856c2-135">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="856c2-135">For authenticated users:</span></span>
  * <span data-ttu-id="856c2-136">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="856c2-136">Displays the current username.</span></span>
  * <span data-ttu-id="856c2-137">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="856c2-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="856c2-138">En el caso de los usuarios anónimos, ofrece la posibilidad de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="856c2-138">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="856c2-139">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="856c2-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="856c2-140">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="856c2-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="856c2-141">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="856c2-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
