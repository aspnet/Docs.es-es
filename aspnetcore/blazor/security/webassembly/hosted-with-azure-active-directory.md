---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 08e202f0876d01a98c099424fd65b06cbe82a3b2
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130345"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="9ac7f-102">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="9ac7f-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="9ac7f-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9ac7f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9ac7f-104">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="9ac7f-105">Registro de aplicaciones en AAD y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="9ac7f-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9ac7f-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="9ac7f-106">Create a tenant</span></span>

<span data-ttu-id="9ac7f-107">Siga las instrucciones que encontrará en [Inicio rápido: Configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9ac7f-108">Registro de una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="9ac7f-108">Register a server API app</span></span>

<span data-ttu-id="9ac7f-109">Siga las instrucciones que encontrará en [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app) y en temas de Azure AAD posteriores para registrar una aplicación de AAD para la *aplicación de API de servidor*. Tras ello, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="9ac7f-110">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9ac7f-111">Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9ac7f-112">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="9ac7f-113">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9ac7f-114">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9ac7f-115">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9ac7f-116">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-116">Select **Register**.</span></span>

<span data-ttu-id="9ac7f-117">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-117">Record the following information:</span></span>

* <span data-ttu-id="9ac7f-118">Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="9ac7f-119">Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="9ac7f-120">Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="9ac7f-121">En **Permisos de API**, quite el permiso **Microsoft Graph** > **User.Read**, ya que la aplicación no requiere inicio de sesión ni acceso de perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="9ac7f-122">En **Exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="9ac7f-123">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9ac7f-124">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9ac7f-125">Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9ac7f-126">Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9ac7f-127">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9ac7f-128">Confirme que **Estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9ac7f-129">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-129">Select **Add scope**.</span></span>

<span data-ttu-id="9ac7f-130">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-130">Record the following information:</span></span>

* <span data-ttu-id="9ac7f-131">URI del identificador de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)</span><span class="sxs-lookup"><span data-stu-id="9ac7f-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9ac7f-132">Ámbito predeterminado (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="9ac7f-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="9ac7f-133">El URI de identificador de aplicación puede requerir una configuración especial en la aplicación cliente, que se describe en la sección [Ámbitos de token de acceso](#access-token-scopes) más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9ac7f-134">Registro de una aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="9ac7f-134">Register a client app</span></span>

<span data-ttu-id="9ac7f-135">Siga las instrucciones que encontrará en [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app) y en temas de Azure AAD posteriores para registrar una aplicación de AAD para la *aplicación cliente*. Tras ello, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="9ac7f-136">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9ac7f-137">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="9ac7f-138">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="9ac7f-139">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9ac7f-140">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9ac7f-141">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9ac7f-142">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9ac7f-143">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación de servidor, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9ac7f-144">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9ac7f-145">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9ac7f-146">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9ac7f-147">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-147">Select **Register**.</span></span>

<span data-ttu-id="9ac7f-148">Registre el identificador de aplicación (cliente) de la *aplicación cliente*; por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="9ac7f-149">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9ac7f-150">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9ac7f-151">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="9ac7f-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9ac7f-152">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9ac7f-153">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-153">Select the **Save** button.</span></span>

<span data-ttu-id="9ac7f-154">En **Permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-154">In **API permissions**:</span></span>

1. <span data-ttu-id="9ac7f-155">Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="9ac7f-156">Seleccione **Agregar un permiso**, seguido de **Mis API**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9ac7f-157">Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9ac7f-158">Abra la lista **API**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-158">Open the **API** list.</span></span>
1. <span data-ttu-id="9ac7f-159">Habilite el acceso a la API (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9ac7f-160">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9ac7f-161">Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** .</span><span class="sxs-lookup"><span data-stu-id="9ac7f-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="9ac7f-162">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9ac7f-163">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-163">Create the app</span></span>

<span data-ttu-id="9ac7f-164">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="9ac7f-165">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="9ac7f-165">Placeholder</span></span>                  | <span data-ttu-id="9ac7f-166">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="9ac7f-166">Azure portal name</span></span>                                     | <span data-ttu-id="9ac7f-167">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="9ac7f-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="9ac7f-168">Identificador de aplicación (cliente) para la *aplicación cliente*</span><span class="sxs-lookup"><span data-stu-id="9ac7f-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="9ac7f-169">Nombre de ámbito</span><span class="sxs-lookup"><span data-stu-id="9ac7f-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="9ac7f-170">Identificador de aplicación (cliente) para la *aplicación de API de servidor*</span><span class="sxs-lookup"><span data-stu-id="9ac7f-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="9ac7f-171">URI de identificador de aplicación ([consultar nota](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="9ac7f-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="9ac7f-172">Dominio Principal/Publicador/Inquilino</span><span class="sxs-lookup"><span data-stu-id="9ac7f-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="9ac7f-173">Id. de directorio (inquilino)</span><span class="sxs-lookup"><span data-stu-id="9ac7f-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="9ac7f-174">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9ac7f-175">Pase el URI de identificador de aplicación a la opción `app-id-uri`, pero tenga en cuenta que puede que deba realizar un cambio de configuración en la aplicación cliente; esto se describe en la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="9ac7f-176">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la *aplicación cliente* se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9ac7f-177">Si la *aplicación cliente* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9ac7f-178">Si el puerto no se configuró anteriormente con el puerto conocido de la *aplicación cliente*, vuelva al registro de la *aplicación cliente* en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9ac7f-179">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="9ac7f-179">Server app configuration</span></span>

<span data-ttu-id="9ac7f-180">*Esta sección atañe a la aplicación **`Server`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="9ac7f-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9ac7f-181">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-181">Authentication package</span></span>

<span data-ttu-id="9ac7f-182">La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/):</span><span class="sxs-lookup"><span data-stu-id="9ac7f-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9ac7f-183">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-183">Authentication service support</span></span>

<span data-ttu-id="9ac7f-184">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9ac7f-185">El método <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="9ac7f-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="9ac7f-187">La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9ac7f-188">Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="9ac7f-189">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="9ac7f-189">User.Identity.Name</span></span>

<span data-ttu-id="9ac7f-190">La API de la aplicación de servidor rellena `User.Identity.Name` de forma predeterminada con el valor del tipo de notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="9ac7f-191">Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`, establezca <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9ac7f-192">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-192">App settings</span></span>

<span data-ttu-id="9ac7f-193">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="9ac7f-194">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-194">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="9ac7f-195">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="9ac7f-195">WeatherForecast controller</span></span>

<span data-ttu-id="9ac7f-196">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="9ac7f-197">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="9ac7f-198">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9ac7f-199">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="9ac7f-200">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="9ac7f-200">Client app configuration</span></span>

<span data-ttu-id="9ac7f-201">*Esta sección atañe a la aplicación **`Client`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="9ac7f-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9ac7f-202">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-202">Authentication package</span></span>

<span data-ttu-id="9ac7f-203">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="9ac7f-204">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9ac7f-205">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9ac7f-206">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9ac7f-207">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-207">Authentication service support</span></span>

<span data-ttu-id="9ac7f-208">Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9ac7f-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="9ac7f-210">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="9ac7f-211">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="9ac7f-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="9ac7f-212">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9ac7f-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9ac7f-214">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9ac7f-215">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="9ac7f-216">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9ac7f-217">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="9ac7f-218">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="9ac7f-218">Access token scopes</span></span>

<span data-ttu-id="9ac7f-219">Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9ac7f-220">Están incluidos de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9ac7f-221">Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9ac7f-222">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9ac7f-223">En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9ac7f-224">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9ac7f-225">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="9ac7f-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9ac7f-226">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="9ac7f-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="9ac7f-227">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="9ac7f-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9ac7f-228">Página de índice</span><span class="sxs-lookup"><span data-stu-id="9ac7f-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9ac7f-229">Componente App</span><span class="sxs-lookup"><span data-stu-id="9ac7f-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9ac7f-230">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9ac7f-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9ac7f-231">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9ac7f-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9ac7f-232">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="9ac7f-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9ac7f-233">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="9ac7f-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9ac7f-234">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="9ac7f-234">Run the app</span></span>

<span data-ttu-id="9ac7f-235">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-235">Run the app from the Server project.</span></span> <span data-ttu-id="9ac7f-236">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ac7f-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9ac7f-237">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9ac7f-238">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="9ac7f-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9ac7f-239">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9ac7f-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="9ac7f-240">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="9ac7f-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9ac7f-241">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="9ac7f-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
