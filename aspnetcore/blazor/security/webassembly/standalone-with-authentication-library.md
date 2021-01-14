---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación.
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a4f3234aa4b4b02244d17615a9033db3094d3580
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024787"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="f9953-103">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="f9953-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="f9953-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f9953-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f9953-105">*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*</span><span class="sxs-lookup"><span data-stu-id="f9953-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="f9953-106">Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), siga las instrucciones para sus herramientas elegidas.</span><span class="sxs-lookup"><span data-stu-id="f9953-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9953-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9953-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f9953-108">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="f9953-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f9953-109">Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="f9953-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="f9953-110">Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9953-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="f9953-111">Visual Studio Code y CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f9953-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="f9953-112">Cree un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía.</span><span class="sxs-lookup"><span data-stu-id="f9953-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="f9953-113">Especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f9953-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="f9953-114">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="f9953-114">Placeholder</span></span>  | <span data-ttu-id="f9953-115">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="f9953-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="f9953-116">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f9953-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="f9953-117">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9953-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f9953-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9953-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f9953-119">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="f9953-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f9953-120">En el paso **Configure your new Blazor WebAssembly App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="f9953-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f9953-121">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [Identity](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="f9953-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="f9953-122">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="f9953-122">Authentication package</span></span>

<span data-ttu-id="f9953-123">Cuando una aplicación se crea para usar cuentas de usuario individuales, dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f9953-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="f9953-124">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="f9953-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f9953-125">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f9953-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="f9953-126">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="f9953-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f9953-127">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="f9953-127">Authentication service support</span></span>

<span data-ttu-id="f9953-128">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> proporcionado por el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="f9953-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="f9953-129">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="f9953-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f9953-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f9953-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="f9953-131">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f9953-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="f9953-132">La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="f9953-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="f9953-133">El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC.</span><span class="sxs-lookup"><span data-stu-id="f9953-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="f9953-134">Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="f9953-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="f9953-135">Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.</span><span class="sxs-lookup"><span data-stu-id="f9953-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f9953-136">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="f9953-136">Access token scopes</span></span>

<span data-ttu-id="f9953-137">La plantilla Blazor WebAssembly configura automáticamente los ámbitos predeterminados para `openid` y `profile`.</span><span class="sxs-lookup"><span data-stu-id="f9953-137">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="f9953-138">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="f9953-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f9953-139">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="f9953-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f9953-140">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="f9953-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f9953-141">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="f9953-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f9953-142">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="f9953-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="f9953-143">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="f9953-143">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f9953-144">Página de índice</span><span class="sxs-lookup"><span data-stu-id="f9953-144">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="f9953-145">Componente App</span><span class="sxs-lookup"><span data-stu-id="f9953-145">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f9953-146">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f9953-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f9953-147">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f9953-147">LoginDisplay component</span></span>

<span data-ttu-id="f9953-148">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="f9953-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="f9953-149">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="f9953-149">For authenticated users:</span></span>
  * <span data-ttu-id="f9953-150">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="f9953-150">Displays the current username.</span></span>
  * <span data-ttu-id="f9953-151">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f9953-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="f9953-152">En el caso de los usuarios anónimos, ofrece la posibilidad de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="f9953-152">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="f9953-153">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="f9953-153">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f9953-154">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f9953-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f9953-155">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="f9953-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="f9953-156"><xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f9953-156"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="f9953-157">Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.</span><span class="sxs-lookup"><span data-stu-id="f9953-157">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="f9953-158">Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.</span><span class="sxs-lookup"><span data-stu-id="f9953-158">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
