---
title: 'Protección de una aplicación independiente :::no-loc(Blazor WebAssembly)::: de ASP.NET Core con la biblioteca de autenticación'
author: guardrex
description: 'Obtenga información sobre cómo proteger una aplicación independiente :::no-loc(Blazor WebAssembly)::: de ASP.NET Core con la biblioteca de autenticación.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: d030e97a3adc17f53b42b98a53f04f155ea93bb6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055118"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="45552-103">Protección de una aplicación independiente :::no-loc(Blazor WebAssembly)::: de ASP.NET Core con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="45552-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with the Authentication library</span></span>

<span data-ttu-id="45552-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="45552-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45552-105">*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*</span><span class="sxs-lookup"><span data-stu-id="45552-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="45552-106">Para crear una [aplicación :::no-loc(Blazor WebAssembly)::: independiente](xref:blazor/hosting-models#blazor-webassembly) que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), siga las instrucciones para sus herramientas elegidas.</span><span class="sxs-lookup"><span data-stu-id="45552-106">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45552-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45552-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="45552-108">Para crear un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="45552-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="45552-109">Después de elegir la plantilla de aplicación de **:::no-loc(Blazor WebAssembly):::** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core** , seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="45552-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="45552-110">Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [:::no-loc(Identity):::](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="45552-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="45552-111">Visual Studio Code y CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="45552-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="45552-112">Cree un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación en una carpeta vacía.</span><span class="sxs-lookup"><span data-stu-id="45552-112">Create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="45552-113">Especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar usuarios dentro de la aplicación mediante el sistema [:::no-loc(Identity):::](xref:security/authentication/identity) de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="45552-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="45552-114">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="45552-114">Placeholder</span></span>  | <span data-ttu-id="45552-115">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="45552-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="45552-116">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45552-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="45552-117">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="45552-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45552-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="45552-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="45552-119">Para crear un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="45552-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="45552-120">En el paso **Configure your new :::no-loc(Blazor WebAssembly)::: App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="45552-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="45552-121">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="45552-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="45552-122">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="45552-122">Authentication package</span></span>

<span data-ttu-id="45552-123">Cuando una aplicación se crea para usar cuentas de usuario individuales, dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45552-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="45552-124">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="45552-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="45552-125">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="45552-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="45552-126">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="45552-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="45552-127">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="45552-127">Authentication service support</span></span>

<span data-ttu-id="45552-128">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> proporcionado por el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="45552-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="45552-129">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de :::no-loc(Identity):::.</span><span class="sxs-lookup"><span data-stu-id="45552-129">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="45552-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45552-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="45552-131">La configuración se suministra a través del archivo `wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="45552-131">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="45552-132">La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="45552-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="45552-133">El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC.</span><span class="sxs-lookup"><span data-stu-id="45552-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="45552-134">Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="45552-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="45552-135">Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.</span><span class="sxs-lookup"><span data-stu-id="45552-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="45552-136">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="45552-136">Access token scopes</span></span>

<span data-ttu-id="45552-137">La plantilla :::no-loc(Blazor WebAssembly)::: configura automáticamente los ámbitos predeterminados para `openid` y `profile`.</span><span class="sxs-lookup"><span data-stu-id="45552-137">The :::no-loc(Blazor WebAssembly)::: template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="45552-138">La plantilla de :::no-loc(Blazor WebAssembly)::: no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="45552-138">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="45552-139">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="45552-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="45552-140">Para más información, vea las siguientes secciones del artículo *Otros escenarios* :</span><span class="sxs-lookup"><span data-stu-id="45552-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="45552-141">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="45552-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="45552-142">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="45552-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="45552-143">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="45552-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="45552-144">Página de índice</span><span class="sxs-lookup"><span data-stu-id="45552-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="45552-145">Componente App</span><span class="sxs-lookup"><span data-stu-id="45552-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="45552-146">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="45552-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="45552-147">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="45552-147">LoginDisplay component</span></span>

<span data-ttu-id="45552-148">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="45552-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="45552-149">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="45552-149">For authenticated users:</span></span>
  * <span data-ttu-id="45552-150">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="45552-150">Displays the current username.</span></span>
  * <span data-ttu-id="45552-151">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45552-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="45552-152">En el caso de los usuarios anónimos, ofrece la posibilidad de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="45552-152">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.:::no-loc(Identity):::.Name!
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

## <a name="authentication-component"></a><span data-ttu-id="45552-153">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="45552-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="45552-154">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="45552-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="45552-155">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="45552-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
