---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con cuentas Microsoft
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 38452c3e9578d8a942035174a28d945e58e4008d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402044"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="5680e-102">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="5680e-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="5680e-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5680e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5680e-104">Para crear una aplicación independiente Blazor WebAssembly que usa [cuentas Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="5680e-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="5680e-105">Creación de un inquilino de AAD y una aplicación web</span><span class="sxs-lookup"><span data-stu-id="5680e-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="5680e-106">Registre una aplicación de AAD en el área **Azure Active Directory** > **Registros de aplicaciones** de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5680e-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5680e-107">Indique un **Nombre** para la aplicación (por ejemplo, **Cuentas Microsoft de AAD independientes de Blazor** ).</span><span class="sxs-lookup"><span data-stu-id="5680e-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="5680e-108">En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier directorio organizativo**.</span><span class="sxs-lookup"><span data-stu-id="5680e-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="5680e-109">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="5680e-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5680e-110">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="5680e-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5680e-111">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5680e-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5680e-112">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="5680e-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5680e-113">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="5680e-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5680e-114">Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="5680e-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5680e-115">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="5680e-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5680e-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="5680e-116">Select **Register**.</span></span>

<span data-ttu-id="5680e-117">Registre el identificador de la aplicación (identificador de cliente); por ejemplo, `11111111-1111-1111-1111-111111111111`.</span><span class="sxs-lookup"><span data-stu-id="5680e-117">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="5680e-118">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="5680e-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5680e-119">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="5680e-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5680e-120">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="5680e-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5680e-121">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="5680e-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5680e-122">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="5680e-122">Select the **Save** button.</span></span>

<span data-ttu-id="5680e-123">Crear la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5680e-123">Create the app.</span></span> <span data-ttu-id="5680e-124">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="5680e-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="5680e-125">Para especificar la ubicación de salida, lo que crea una carpeta de proyecto si no existe, incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="5680e-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5680e-126">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5680e-126">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5680e-127">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5680e-127">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="5680e-128">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="5680e-128">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="5680e-129">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="5680e-129">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="5680e-130">Después de crear la aplicación, debe poder hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5680e-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="5680e-131">Iniciar sesión en la aplicación con una cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="5680e-131">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="5680e-132">Solicitar tokens de acceso para API de Microsoft</span><span class="sxs-lookup"><span data-stu-id="5680e-132">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="5680e-133">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="5680e-133">For more information, see:</span></span>
  * [<span data-ttu-id="5680e-134">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="5680e-134">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="5680e-135">[Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="5680e-135">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5680e-136">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="5680e-136">Authentication package</span></span>

<span data-ttu-id="5680e-137">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="5680e-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="5680e-138">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="5680e-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5680e-139">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5680e-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="5680e-140">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="5680e-140">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5680e-141">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="5680e-141">Authentication service support</span></span>

<span data-ttu-id="5680e-142">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="5680e-142">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="5680e-143">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="5680e-143">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5680e-144">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="5680e-144">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="5680e-145">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5680e-145">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5680e-146">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5680e-146">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="5680e-147">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="5680e-147">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="5680e-148">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5680e-148">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="5680e-149">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="5680e-149">Access token scopes</span></span>

<span data-ttu-id="5680e-150">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="5680e-150">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5680e-151">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="5680e-151">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="5680e-152">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="5680e-152">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5680e-153">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="5680e-153">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5680e-154">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="5680e-154">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="5680e-155">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="5680e-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5680e-156">Página de índice</span><span class="sxs-lookup"><span data-stu-id="5680e-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5680e-157">Componente App</span><span class="sxs-lookup"><span data-stu-id="5680e-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5680e-158">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5680e-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5680e-159">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5680e-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5680e-160">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="5680e-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5680e-161">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5680e-161">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="5680e-162">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="5680e-162">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="5680e-163">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="5680e-163">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="5680e-164">Inicio rápido: Configuración de una aplicación para exponer las API web</span><span class="sxs-lookup"><span data-stu-id="5680e-164">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
