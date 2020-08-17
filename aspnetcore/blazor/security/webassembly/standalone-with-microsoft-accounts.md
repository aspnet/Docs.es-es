---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con cuentas Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8f48165e2501ea17de6d5cd540f1d6ff088d3173
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130280"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="b12eb-102">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con cuentas Microsoft</span><span class="sxs-lookup"><span data-stu-id="b12eb-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="b12eb-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b12eb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b12eb-104">Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que usa cuentas [Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="b12eb-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="b12eb-105">Creación de un inquilino de AAD y una aplicación web</span><span class="sxs-lookup"><span data-stu-id="b12eb-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="b12eb-106">Registre una aplicación de AAD en el área **Azure Active Directory** > **Registros de aplicaciones** de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b12eb-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b12eb-107">Indique un **Nombre** para la aplicación (por ejemplo, **Cuentas Microsoft de AAD independientes de Blazor** ).</span><span class="sxs-lookup"><span data-stu-id="b12eb-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="b12eb-108">En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier directorio organizativo**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="b12eb-109">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="b12eb-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b12eb-110">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="b12eb-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b12eb-111">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b12eb-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b12eb-112">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b12eb-113">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="b12eb-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b12eb-114">Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="b12eb-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b12eb-115">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b12eb-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-116">Select **Register**.</span></span>

<span data-ttu-id="b12eb-117">Registre el identificador de aplicación (cliente); por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="b12eb-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="b12eb-118">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="b12eb-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b12eb-119">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="b12eb-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b12eb-120">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="b12eb-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b12eb-121">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="b12eb-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b12eb-122">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-122">Select the **Save** button.</span></span>

<span data-ttu-id="b12eb-123">Crear la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b12eb-123">Create the app.</span></span> <span data-ttu-id="b12eb-124">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b12eb-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="b12eb-125">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="b12eb-125">Placeholder</span></span>   | <span data-ttu-id="b12eb-126">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="b12eb-126">Azure portal name</span></span>       | <span data-ttu-id="b12eb-127">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b12eb-127">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="b12eb-128">Id. de aplicación (cliente)</span><span class="sxs-lookup"><span data-stu-id="b12eb-128">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="b12eb-129">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b12eb-129">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b12eb-130">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b12eb-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b12eb-131">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="b12eb-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b12eb-132">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="b12eb-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="b12eb-133">Después de crear la aplicación, debe poder hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b12eb-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="b12eb-134">Iniciar sesión en la aplicación con una cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="b12eb-134">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="b12eb-135">Solicitar tokens de acceso para API de Microsoft</span><span class="sxs-lookup"><span data-stu-id="b12eb-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="b12eb-136">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b12eb-136">For more information, see:</span></span>
  * [<span data-ttu-id="b12eb-137">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="b12eb-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="b12eb-138">[Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="b12eb-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b12eb-139">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="b12eb-139">Authentication package</span></span>

<span data-ttu-id="b12eb-140">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="b12eb-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b12eb-141">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="b12eb-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b12eb-142">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b12eb-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b12eb-143">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="b12eb-143">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b12eb-144">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="b12eb-144">Authentication service support</span></span>

<span data-ttu-id="b12eb-145">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="b12eb-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b12eb-146">Este método configura todos los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="b12eb-146">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b12eb-147">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b12eb-147">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="b12eb-148">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b12eb-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b12eb-149">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b12eb-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="b12eb-150">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b12eb-150">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b12eb-151">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b12eb-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="b12eb-152">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="b12eb-152">Access token scopes</span></span>

<span data-ttu-id="b12eb-153">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="b12eb-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b12eb-154">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="b12eb-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b12eb-155">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="b12eb-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b12eb-156">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="b12eb-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b12eb-157">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="b12eb-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b12eb-158">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="b12eb-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b12eb-159">Página de índice</span><span class="sxs-lookup"><span data-stu-id="b12eb-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b12eb-160">Componente App</span><span class="sxs-lookup"><span data-stu-id="b12eb-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b12eb-161">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b12eb-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b12eb-162">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b12eb-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b12eb-163">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="b12eb-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b12eb-164">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b12eb-164">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="b12eb-165">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="b12eb-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="b12eb-166">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="b12eb-166">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="b12eb-167">Inicio rápido: Configuración de una aplicación para exponer las API web</span><span class="sxs-lookup"><span data-stu-id="b12eb-167">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
