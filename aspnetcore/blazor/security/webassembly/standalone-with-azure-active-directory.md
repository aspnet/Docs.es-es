---
title: Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: fa73075650a23d90e2e546335c06c4d50a29d34a
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130267"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="cc656-102">Protección de una aplicación independiente Blazor WebAssembly de ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="cc656-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="cc656-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cc656-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cc656-104">Para crear una [aplicación Blazor WebAssembly independiente](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="cc656-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="cc656-105">[Creación de un inquilino de AAD y una aplicación web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="cc656-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="cc656-106">Registre una aplicación de AAD en el área **Azure Active Directory** > **Registros de aplicaciones** de Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="cc656-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="cc656-107">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Standalone AAD**).</span><span class="sxs-lookup"><span data-stu-id="cc656-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="cc656-108">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="cc656-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="cc656-109">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo**.</span><span class="sxs-lookup"><span data-stu-id="cc656-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="cc656-110">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="cc656-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cc656-111">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="cc656-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cc656-112">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc656-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="cc656-113">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="cc656-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="cc656-114">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="cc656-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="cc656-115">Más adelante en este tema aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="cc656-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="cc656-116">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="cc656-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cc656-117">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="cc656-117">Select **Register**.</span></span>

<span data-ttu-id="cc656-118">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="cc656-118">Record the following information:</span></span>

* <span data-ttu-id="cc656-119">Identificador de aplicación (cliente); por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="cc656-119">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="cc656-120">Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.</span><span class="sxs-lookup"><span data-stu-id="cc656-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="cc656-121">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="cc656-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="cc656-122">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="cc656-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cc656-123">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="cc656-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="cc656-124">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="cc656-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cc656-125">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="cc656-125">Select the **Save** button.</span></span>

<span data-ttu-id="cc656-126">Cree la aplicación en una carpeta vacía.</span><span class="sxs-lookup"><span data-stu-id="cc656-126">Create the app in an empty folder.</span></span> <span data-ttu-id="cc656-127">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="cc656-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="cc656-128">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="cc656-128">Placeholder</span></span>   | <span data-ttu-id="cc656-129">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="cc656-129">Azure portal name</span></span>       | <span data-ttu-id="cc656-130">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="cc656-130">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="cc656-131">Id. de aplicación (cliente)</span><span class="sxs-lookup"><span data-stu-id="cc656-131">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="cc656-132">Id. de directorio (inquilino)</span><span class="sxs-lookup"><span data-stu-id="cc656-132">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="cc656-133">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc656-133">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="cc656-134">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cc656-134">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="cc656-135">Si la aplicación se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la aplicación, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="cc656-135">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="cc656-136">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación, vuelva al registro de la aplicación en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="cc656-136">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="cc656-137">Después de crear la aplicación, debe poder hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="cc656-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="cc656-138">Iniciar sesión en la aplicación con una cuenta de usuario de AAD</span><span class="sxs-lookup"><span data-stu-id="cc656-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="cc656-139">Solicitar tokens de acceso para API de Microsoft</span><span class="sxs-lookup"><span data-stu-id="cc656-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="cc656-140">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="cc656-140">For more information, see:</span></span>
  * [<span data-ttu-id="cc656-141">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="cc656-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="cc656-142">[Inicio rápido: Configuración de una aplicación para exponer las API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="cc656-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cc656-143">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="cc656-143">Authentication package</span></span>

<span data-ttu-id="cc656-144">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="cc656-144">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="cc656-145">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="cc656-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cc656-146">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="cc656-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="cc656-147">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="cc656-147">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cc656-148">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="cc656-148">Authentication service support</span></span>

<span data-ttu-id="cc656-149">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="cc656-149">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="cc656-150">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="cc656-150">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cc656-151">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="cc656-151">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="cc656-152">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc656-152">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cc656-153">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc656-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="cc656-154">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="cc656-154">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="cc656-155">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cc656-155">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="cc656-156">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="cc656-156">Access token scopes</span></span>

<span data-ttu-id="cc656-157">La plantilla de Blazor WebAssembly no configura la aplicación automáticamente para solicitar un token de acceso relativo a una API segura.</span><span class="sxs-lookup"><span data-stu-id="cc656-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cc656-158">Para aprovisionar un token de acceso como parte del flujo de inicio de sesión, agregue el ámbito a los ámbitos de token de acceso predeterminados de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="cc656-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="cc656-159">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="cc656-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="cc656-160">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="cc656-160">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cc656-161">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="cc656-161">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="cc656-162">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="cc656-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cc656-163">Página de índice</span><span class="sxs-lookup"><span data-stu-id="cc656-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cc656-164">Componente App</span><span class="sxs-lookup"><span data-stu-id="cc656-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cc656-165">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="cc656-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cc656-166">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="cc656-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cc656-167">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="cc656-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cc656-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cc656-168">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="cc656-169">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="cc656-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="cc656-170">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="cc656-170">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
