---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280887"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="8bb26-103">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con la biblioteca de autenticación</span><span class="sxs-lookup"><span data-stu-id="8bb26-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="8bb26-104">*No siga las instrucciones de este tema con Azure Active Directory (AAD) ni Azure Active Directory B2C (AAD B2C). Vea los temas relativos a AAD y AAD B2C del índice.*</span><span class="sxs-lookup"><span data-stu-id="8bb26-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="8bb26-105">Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que use la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), siga las instrucciones para sus herramientas elegidas.</span><span class="sxs-lookup"><span data-stu-id="8bb26-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span> <span data-ttu-id="8bb26-106">Si va a agregar compatibilidad con la autenticación, consulte las siguientes secciones de este artículo para obtener instrucciones sobre cómo instalar y configurar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8bb26-106">If adding support for authentication, see the following sections of this article for guidance on setting up and configuring the app.</span></span>

> [!NOTE]
> <span data-ttu-id="8bb26-107">El proveedor (IP) Identity debe usar [OpenID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="8bb26-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="8bb26-108">Por ejemplo, la dirección IP de Facebook no es un proveedor compatible con OIDC, por lo que las instrucciones de este tema no funcionan con la dirección IP de Facebook.</span><span class="sxs-lookup"><span data-stu-id="8bb26-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="8bb26-109">Para obtener más información, vea <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="8bb26-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bb26-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bb26-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8bb26-111">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8bb26-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="8bb26-112">Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="8bb26-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="8bb26-113">Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para usar el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8bb26-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="8bb26-114">Esta selección agrega compatibilidad con la autenticación y no conlleva el almacenamiento de usuarios en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8bb26-114">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="8bb26-115">En las siguientes secciones de este artículo se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="8bb26-115">The following sections of this article provide further details.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="8bb26-116">Visual Studio Code y CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="8bb26-116">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="8bb26-117">Cree un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía.</span><span class="sxs-lookup"><span data-stu-id="8bb26-117">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="8bb26-118">Especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para usar el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8bb26-118">Specify the `Individual` authentication mechanism with the `-au|--auth` option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="8bb26-119">Esta selección agrega compatibilidad con la autenticación y no conlleva el almacenamiento de usuarios en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8bb26-119">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="8bb26-120">En las siguientes secciones de este artículo se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="8bb26-120">The following sections of this article provide further details.</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="8bb26-121">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="8bb26-121">Placeholder</span></span>  | <span data-ttu-id="8bb26-122">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="8bb26-122">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="8bb26-123">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8bb26-123">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="8bb26-124">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8bb26-124">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bb26-125">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8bb26-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8bb26-126">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8bb26-126">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="8bb26-127">En el paso **Configure your new Blazor WebAssembly App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="8bb26-127">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="8bb26-128">La aplicación se crea para usar [Identity](xref:security/authentication/identity) de ASP.NET Core y no conlleva el almacenamiento de usuarios en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8bb26-128">The app is created to use ASP.NET Core [Identity](xref:security/authentication/identity) and doesn't result in storing users in a database.</span></span> <span data-ttu-id="8bb26-129">En las siguientes secciones de este artículo se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="8bb26-129">The following sections of this article provide further details.</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="8bb26-130">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="8bb26-130">Authentication package</span></span>

<span data-ttu-id="8bb26-131">Cuando una aplicación se crea para usar cuentas de usuario individuales, dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8bb26-131">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="8bb26-132">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="8bb26-132">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8bb26-133">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8bb26-133">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="8bb26-134">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="8bb26-134">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="8bb26-135">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="8bb26-135">Authentication service support</span></span>

<span data-ttu-id="8bb26-136">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> proporcionado por el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="8bb26-136">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="8bb26-137">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="8bb26-137">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8bb26-138">Para una nueva aplicación, proporcione valores para los marcadores de posición `{AUTHORITY}` y `{CLIENT ID}` en la siguiente configuración.</span><span class="sxs-lookup"><span data-stu-id="8bb26-138">For a new app, provide values for the `{AUTHORITY}` and `{CLIENT ID}` placeholders in the following configuration.</span></span> <span data-ttu-id="8bb26-139">Proporcione otros valores de configuración que sean necesarios para su uso con la dirección IP de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8bb26-139">Provide other configuration values that are required for use with the app's IP.</span></span> <span data-ttu-id="8bb26-140">El ejemplo es para Google, que requiere `PostLogoutRedirectUri`, `RedirectUri` y `ResponseType`.</span><span class="sxs-lookup"><span data-stu-id="8bb26-140">The example is for Google, which requires `PostLogoutRedirectUri`, `RedirectUri`, and `ResponseType`.</span></span> <span data-ttu-id="8bb26-141">Si incluye autenticación en una aplicación, agregue manualmente el código y la configuración siguientes a la aplicación con valores para los marcadores de posición y otros valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="8bb26-141">If adding authentication to an app, manually add the following code and configuration to the app with values for the placeholders and other configuration values.</span></span>

<span data-ttu-id="8bb26-142">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="8bb26-142">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="8bb26-143">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="8bb26-143">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="8bb26-144">Ejemplo OIDC de Google OAuth 2.0:</span><span class="sxs-lookup"><span data-stu-id="8bb26-144">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="8bb26-145">El URI de redireccionamiento (`https://localhost:5001/authentication/login-callback`) se registra en la [consola de API de Google](https://console.developers.google.com/apis/dashboard) en **Credenciales** >  **`{NAME}`**  > **URIs de redireccionamiento autorizados**, donde `{NAME}` es el nombre de cliente de la aplicación en la lista **Identificadores de cliente de OAuth 2.0** de la consola de API de Google.</span><span class="sxs-lookup"><span data-stu-id="8bb26-145">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="8bb26-146">La compatibilidad con la autenticación de aplicaciones independientes se ofrece por medio de OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="8bb26-146">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="8bb26-147">El método <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación por medio de OIDC.</span><span class="sxs-lookup"><span data-stu-id="8bb26-147">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="8bb26-148">Los valores necesarios para configurar la aplicación se pueden obtener a partir del proveedor de identidades compatible con OIDC.</span><span class="sxs-lookup"><span data-stu-id="8bb26-148">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="8bb26-149">Obtenga los valores cuando registre la aplicación, lo que suele realizarse en el portal en línea del proveedor de identidades en cuestión.</span><span class="sxs-lookup"><span data-stu-id="8bb26-149">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="8bb26-150">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="8bb26-150">Access token scopes</span></span>

<span data-ttu-id="8bb26-151">La plantilla Blazor WebAssembly configura automáticamente los ámbitos predeterminados para `openid` y `profile`.</span><span class="sxs-lookup"><span data-stu-id="8bb26-151">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="8bb26-152">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="8bb26-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8bb26-153">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token predeterminados de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="8bb26-153">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span></span> <span data-ttu-id="8bb26-154">Si incluye autenticación en una aplicación, agregue manualmente el código siguiente y configure el URI de ámbito.</span><span class="sxs-lookup"><span data-stu-id="8bb26-154">If adding authentication to an app, manually add the following code and configure the scope URI.</span></span>

<span data-ttu-id="8bb26-155">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="8bb26-155">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="8bb26-156">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="8bb26-156">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="8bb26-157">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="8bb26-157">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8bb26-158">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="8bb26-158">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="8bb26-159">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="8bb26-159">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8bb26-160">Página de índice</span><span class="sxs-lookup"><span data-stu-id="8bb26-160">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="8bb26-161">Componente App</span><span class="sxs-lookup"><span data-stu-id="8bb26-161">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8bb26-162">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="8bb26-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8bb26-163">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="8bb26-163">LoginDisplay component</span></span>

<span data-ttu-id="8bb26-164">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="8bb26-164">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="8bb26-165">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="8bb26-165">For authenticated users:</span></span>
  * <span data-ttu-id="8bb26-166">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="8bb26-166">Displays the current username.</span></span>
  * <span data-ttu-id="8bb26-167">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8bb26-167">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="8bb26-168">En el caso de los usuarios anónimos, ofrece la posibilidad de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="8bb26-168">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="8bb26-169">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="8bb26-169">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8bb26-170">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8bb26-170">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="8bb26-171">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="8bb26-171">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="8bb26-172"><xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8bb26-172"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="8bb26-173">Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.</span><span class="sxs-lookup"><span data-stu-id="8bb26-173">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="8bb26-174">Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.</span><span class="sxs-lookup"><span data-stu-id="8bb26-174">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
