---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: f98afc3d5dd73dca23be9a9c1202802f270bcee7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402122"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="d7eea-102">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d7eea-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="d7eea-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7eea-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d7eea-104">Para crear una aplicación independiente Blazor WebAssembly que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="d7eea-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="d7eea-105">Siga las instrucciones de los siguientes temas para crear un inquilino y registrar una aplicación web en Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="d7eea-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="d7eea-106">Creación de un inquilino de AAD B2C</span><span class="sxs-lookup"><span data-stu-id="d7eea-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="d7eea-107">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="d7eea-107">Record the following information:</span></span>

* <span data-ttu-id="d7eea-108">Instancia de AAD B2C (por ejemplo, `https://contoso.b2clogin.com/`, barra diagonal final incluida)</span><span class="sxs-lookup"><span data-stu-id="d7eea-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="d7eea-109">Dominio del inquilino de AAD B2C (por ejemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="d7eea-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="d7eea-110">Siga de nuevo las instrucciones que encontrará en [Tutorial: Registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación cliente* y, después, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d7eea-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="d7eea-111">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="d7eea-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d7eea-112">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Standalone AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="d7eea-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="d7eea-113">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o proveedor de identidades. Elija esta opción para realizar la autenticación con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="d7eea-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="d7eea-114">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d7eea-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="d7eea-115">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="d7eea-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="d7eea-116">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d7eea-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="d7eea-117">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="d7eea-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="d7eea-118">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="d7eea-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="d7eea-119">Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="d7eea-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="d7eea-120">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="d7eea-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d7eea-121">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="d7eea-121">Select **Register**.</span></span>

<span data-ttu-id="d7eea-122">Registre el identificador de la aplicación (identificador de cliente); por ejemplo, `11111111-1111-1111-1111-111111111111`.</span><span class="sxs-lookup"><span data-stu-id="d7eea-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="d7eea-123">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="d7eea-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d7eea-124">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="d7eea-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d7eea-125">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="d7eea-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d7eea-126">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="d7eea-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d7eea-127">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="d7eea-127">Select the **Save** button.</span></span>

<span data-ttu-id="d7eea-128">En **Inicio** > **Azure AD B2C** > **Flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="d7eea-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="d7eea-129">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="d7eea-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="d7eea-130">Como mínimo, seleccione el atributo de usuario **Notificaciones de la aplicación** > **Nombre para mostrar** para rellenar `context.User.Identity.Name` en el componente `LoginDisplay` (`Shared/LoginDisplay.razor`).</span><span class="sxs-lookup"><span data-stu-id="d7eea-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="d7eea-131">Registre el nombre de flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="d7eea-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="d7eea-132">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="d7eea-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="d7eea-133">Para especificar la ubicación de salida, lo que crea una carpeta de proyecto si no existe, incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d7eea-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d7eea-134">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d7eea-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d7eea-135">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d7eea-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="d7eea-136">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="d7eea-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="d7eea-137">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="d7eea-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="d7eea-138">Después de crear la aplicación, debe poder hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d7eea-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="d7eea-139">Iniciar sesión en la aplicación con una cuenta de usuario de AAD</span><span class="sxs-lookup"><span data-stu-id="d7eea-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="d7eea-140">Solicitar tokens de acceso para API de Microsoft</span><span class="sxs-lookup"><span data-stu-id="d7eea-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="d7eea-141">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d7eea-141">For more information, see:</span></span>
  * [<span data-ttu-id="d7eea-142">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="d7eea-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="d7eea-143">[Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="d7eea-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="d7eea-144">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="d7eea-144">Authentication package</span></span>

<span data-ttu-id="d7eea-145">Cuando una aplicación se crea para usar una cuenta de B2C individual (`IndividualB2C`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="d7eea-145">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="d7eea-146">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="d7eea-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d7eea-147">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d7eea-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="d7eea-148">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="d7eea-148">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d7eea-149">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="d7eea-149">Authentication service support</span></span>

<span data-ttu-id="d7eea-150">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="d7eea-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="d7eea-151">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="d7eea-151">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d7eea-152">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d7eea-152">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="d7eea-153">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="d7eea-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d7eea-154">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d7eea-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="d7eea-155">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="d7eea-155">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="d7eea-156">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d7eea-156">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="d7eea-157">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="d7eea-157">Access token scopes</span></span>

<span data-ttu-id="d7eea-158">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="d7eea-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d7eea-159">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="d7eea-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="d7eea-160">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="d7eea-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d7eea-161">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="d7eea-161">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d7eea-162">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="d7eea-162">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="d7eea-163">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="d7eea-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d7eea-164">Página de índice</span><span class="sxs-lookup"><span data-stu-id="d7eea-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="d7eea-165">Componente App</span><span class="sxs-lookup"><span data-stu-id="d7eea-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d7eea-166">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d7eea-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d7eea-167">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d7eea-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="d7eea-168">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="d7eea-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d7eea-169">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d7eea-169">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="d7eea-170">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="d7eea-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="d7eea-171">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d7eea-171">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="d7eea-172">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="d7eea-172">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
