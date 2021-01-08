---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: dac2203d63b2d924ee6ae4f7012e9c33739e6213
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792064"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="50c8d-103">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="50c8d-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="50c8d-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50c8d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50c8d-105">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="50c8d-106">Registro de aplicaciones en AAD B2C y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="50c8d-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="50c8d-107">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="50c8d-107">Create a tenant</span></span>

<span data-ttu-id="50c8d-108">Siga las instrucciones que encontrará en [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="50c8d-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="50c8d-109">Vuelva a este artículo inmediatamente después de crear o identificar un inquilino que vaya a usar.</span><span class="sxs-lookup"><span data-stu-id="50c8d-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="50c8d-110">Registre la instancia de AAD B2C (por ejemplo, `https://contoso.b2clogin.com/`, barra diagonal final incluida).</span><span class="sxs-lookup"><span data-stu-id="50c8d-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="50c8d-111">La instancia es el esquema y el host de un registro de la aplicación de Azure B2C, que se puede encontrar abriendo la ventana **Puntos de conexión** de la página **Registros de aplicaciones** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="50c8d-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="50c8d-112">Registro de una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="50c8d-112">Register a server API app</span></span>

<span data-ttu-id="50c8d-113">Registre una aplicación AAD B2C para la *aplicación de API de servidor*:</span><span class="sxs-lookup"><span data-stu-id="50c8d-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="50c8d-114">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="50c8d-115">Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="50c8d-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="50c8d-116">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**</span><span class="sxs-lookup"><span data-stu-id="50c8d-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="50c8d-117">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="50c8d-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="50c8d-118">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="50c8d-119">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-119">Select **Register**.</span></span>

<span data-ttu-id="50c8d-120">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="50c8d-120">Record the following information:</span></span>

* <span data-ttu-id="50c8d-121">Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="50c8d-122">Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="50c8d-123">En **Exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="50c8d-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="50c8d-124">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="50c8d-125">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="50c8d-126">Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="50c8d-127">Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="50c8d-128">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="50c8d-129">Confirme que **Estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="50c8d-130">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-130">Select **Add scope**.</span></span>

<span data-ttu-id="50c8d-131">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="50c8d-131">Record the following information:</span></span>

* <span data-ttu-id="50c8d-132">URI del identificador de aplicación (por ejemplo, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)</span><span class="sxs-lookup"><span data-stu-id="50c8d-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="50c8d-133">Nombre del ámbito (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="50c8d-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="50c8d-134">Registro de una aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="50c8d-134">Register a client app</span></span>

<span data-ttu-id="50c8d-135">Registre una aplicación AAD B2C para la *aplicación cliente*:</span><span class="sxs-lookup"><span data-stu-id="50c8d-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="50c8d-136">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="50c8d-137">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="50c8d-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="50c8d-138">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**</span><span class="sxs-lookup"><span data-stu-id="50c8d-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="50c8d-139">Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="50c8d-140">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="50c8d-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="50c8d-141">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="50c8d-142">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="50c8d-143">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="50c8d-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="50c8d-144">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="50c8d-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="50c8d-145">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="50c8d-146">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-146">Select **Register**.</span></span>

1. <span data-ttu-id="50c8d-147">Registre el identificador de aplicación (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="50c8d-148">En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única**:</span><span class="sxs-lookup"><span data-stu-id="50c8d-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="50c8d-149">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="50c8d-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="50c8d-150">En **Concesión implícita**, asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="50c8d-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="50c8d-151">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="50c8d-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="50c8d-152">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="50c8d-153">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="50c8d-154">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="50c8d-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="50c8d-155">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o cualquier proveedor de identidades (para la autenticación de usuarios con flujos de usuario)**</span><span class="sxs-lookup"><span data-stu-id="50c8d-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="50c8d-156">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="50c8d-157">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="50c8d-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="50c8d-158">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="50c8d-159">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="50c8d-160">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="50c8d-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="50c8d-161">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="50c8d-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="50c8d-162">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="50c8d-163">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-163">Select **Register**.</span></span>

<span data-ttu-id="50c8d-164">Registre el identificador de aplicación (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="50c8d-165">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="50c8d-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="50c8d-166">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="50c8d-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="50c8d-167">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="50c8d-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="50c8d-168">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="50c8d-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="50c8d-169">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="50c8d-170">En **Permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="50c8d-170">In **API permissions**:</span></span>

1. <span data-ttu-id="50c8d-171">Seleccione **Agregar un permiso**, seguido de **Mis API**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="50c8d-172">Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="50c8d-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="50c8d-173">Abra la lista **API**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-173">Open the **API** list.</span></span>
1. <span data-ttu-id="50c8d-174">Habilite el acceso a la API (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="50c8d-175">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="50c8d-176">Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** .</span><span class="sxs-lookup"><span data-stu-id="50c8d-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="50c8d-177">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="50c8d-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="50c8d-178">En **Inicio** > **Azure AD B2C** > **Flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="50c8d-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="50c8d-179">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="50c8d-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="50c8d-180">Como mínimo, seleccione el atributo de usuario **Notificaciones de la aplicación** > **Nombre para mostrar** para rellenar `context.User.Identity.Name` en el componente `LoginDisplay` (`Shared/LoginDisplay.razor`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="50c8d-181">Registre el nombre de flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="50c8d-182">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="50c8d-182">Create the app</span></span>

<span data-ttu-id="50c8d-183">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="50c8d-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="50c8d-184">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="50c8d-184">Placeholder</span></span>                   | <span data-ttu-id="50c8d-185">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="50c8d-185">Azure portal name</span></span>                                     | <span data-ttu-id="50c8d-186">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="50c8d-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="50c8d-187">Instancia</span><span class="sxs-lookup"><span data-stu-id="50c8d-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="50c8d-188">Identificador de aplicación (cliente) para la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="50c8d-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="50c8d-189">Nombre de ámbito</span><span class="sxs-lookup"><span data-stu-id="50c8d-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="50c8d-190">Identificador de aplicación (cliente) para la *aplicación de API de servidor*</span><span class="sxs-lookup"><span data-stu-id="50c8d-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="50c8d-191">URI de Id. de aplicación&dagger;</span><span class="sxs-lookup"><span data-stu-id="50c8d-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="50c8d-192">Flujo de usuario de registro o de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="50c8d-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="50c8d-193">Dominio Principal/Publicador/Inquilino</span><span class="sxs-lookup"><span data-stu-id="50c8d-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="50c8d-194">&dagger;La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="50c8d-195">Al proporcionar el URI de identificador de aplicación para el marcador de posición `{SERVER API APP ID URI}` y si el esquema es `api://`, quite el esquema (`api://`) del argumento, como se muestra en el valor de ejemplo de la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="50c8d-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="50c8d-196">Si el URI de identificador de aplicación es un valor personalizado o tiene algún otro esquema (por ejemplo, `https://` para un dominio de publicador que no es de confianza similar a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), debe actualizar manualmente el URI del ámbito predeterminado y quitar el esquema `api://` después de que la plantilla cree la aplicación *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="50c8d-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="50c8d-197">Para obtener más información, consulte la nota de la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="50c8d-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="50c8d-198">La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="50c8d-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="50c8d-199">Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="50c8d-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="50c8d-200">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="50c8d-201">El ámbito configurado por la plantilla de Blazor hospedada podría tener repetido el host de URI de identificador de aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="50c8d-202">Confirme que el ámbito configurado para la colección `DefaultAccessTokenScopes` es correcto en `Program.Main` (`Program.cs`) de la aplicación *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="50c8d-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="50c8d-203">En Azure Portal, el valor de configuración de plataforma de la aplicación *`Client`* **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="50c8d-204">Si la aplicación *`Client`* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="50c8d-205">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación *`Client`* , vuelva al registro de la aplicación *`Client`* en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="50c8d-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="50c8d-206">Configuración de la aplicación *`Server`*</span><span class="sxs-lookup"><span data-stu-id="50c8d-206">*`Server`* app configuration</span></span>

<span data-ttu-id="50c8d-207">*Esta sección atañe a la aplicación **`Server`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="50c8d-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="50c8d-208">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="50c8d-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="50c8d-209">La compatibilidad con la autenticación y autorización de llamadas realizadas a las API web de ASP.NET Core con Microsoft Identity Platform se proporciona mediante los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="50c8d-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="50c8d-210">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="50c8d-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="50c8d-211">La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI):</span><span class="sxs-lookup"><span data-stu-id="50c8d-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="50c8d-212">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="50c8d-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="50c8d-213">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="50c8d-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="50c8d-214">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="50c8d-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="50c8d-215">El método <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> configura servicios para proteger la API web con Microsoft Identity Platform v2.0.</span><span class="sxs-lookup"><span data-stu-id="50c8d-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="50c8d-216">Este método espera una sección `AzureAdB2C` en la configuración de la aplicación con los ajustes necesarios para inicializar las opciones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="50c8d-217">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="50c8d-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="50c8d-218">El método <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="50c8d-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="50c8d-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="50c8d-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="50c8d-220">La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="50c8d-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="50c8d-221">Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="50c8d-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="50c8d-222">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="50c8d-222">User.Identity.Name</span></span>

<span data-ttu-id="50c8d-223">`User.Identity.Name` no se rellena de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="50c8d-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="50c8d-224">Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="50c8d-225">Agregue un espacio de nombres de <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> a `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="50c8d-226">Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="50c8d-227">Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="50c8d-228">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="50c8d-228">App settings</span></span>

<span data-ttu-id="50c8d-229">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="50c8d-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="50c8d-230">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="50c8d-230">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="50c8d-231">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="50c8d-231">WeatherForecast controller</span></span>

<span data-ttu-id="50c8d-232">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="50c8d-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="50c8d-233">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="50c8d-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="50c8d-234">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.</span><span class="sxs-lookup"><span data-stu-id="50c8d-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="50c8d-235">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="50c8d-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="50c8d-236">Configuración de la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="50c8d-236">*`Client`* app configuration</span></span>

<span data-ttu-id="50c8d-237">*Esta sección atañe a la aplicación **`Client`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="50c8d-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="50c8d-238">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="50c8d-238">Authentication package</span></span>

<span data-ttu-id="50c8d-239">Cuando una aplicación se crea para usar una cuenta de B2C individual (`IndividualB2C`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="50c8d-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="50c8d-240">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="50c8d-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="50c8d-241">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="50c8d-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="50c8d-242">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="50c8d-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="50c8d-243">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="50c8d-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="50c8d-244">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="50c8d-244">Authentication service support</span></span>

<span data-ttu-id="50c8d-245">Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="50c8d-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="50c8d-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="50c8d-247">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="50c8d-248">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="50c8d-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="50c8d-249">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="50c8d-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="50c8d-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="50c8d-251">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="50c8d-252">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="50c8d-253">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="50c8d-254">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="50c8d-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="50c8d-255">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="50c8d-255">Access token scopes</span></span>

<span data-ttu-id="50c8d-256">Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="50c8d-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="50c8d-257">Están incluidos de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="50c8d-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="50c8d-258">Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="50c8d-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="50c8d-259">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="50c8d-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="50c8d-260">En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:</span><span class="sxs-lookup"><span data-stu-id="50c8d-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="50c8d-261">La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="50c8d-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="50c8d-262">Al generar una aplicación a partir de la plantilla de proyecto Blazor, confirme que el valor del ámbito de token de acceso predeterminado usa el valor de URI de identificador de aplicación personalizado correcto que proporcionó en Azure Portal o un valor con **uno** de los siguientes formatos:</span><span class="sxs-lookup"><span data-stu-id="50c8d-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="50c8d-263">Cuando el dominio del publicador del directorio es **de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:</span><span class="sxs-lookup"><span data-stu-id="50c8d-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="50c8d-264">Inspeccione el valor de un esquema doble (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="50c8d-265">Si hay un esquema doble, quite el primer esquema `api://` del valor.</span><span class="sxs-lookup"><span data-stu-id="50c8d-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="50c8d-266">Cuando el dominio del publicador del directorio **no es de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:</span><span class="sxs-lookup"><span data-stu-id="50c8d-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="50c8d-267">Inspeccione el valor para ver si hay un esquema `api://` adicional (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="50c8d-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="50c8d-268">Si hay un esquema `api://` adicional, quite el esquema `api://` del valor.</span><span class="sxs-lookup"><span data-stu-id="50c8d-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="50c8d-269">La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="50c8d-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="50c8d-270">Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="50c8d-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="50c8d-271">Especifique ámbitos adicionales con `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="50c8d-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="50c8d-272">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="50c8d-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="50c8d-273">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="50c8d-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="50c8d-274">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="50c8d-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="50c8d-275">Modo de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="50c8d-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="50c8d-276">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="50c8d-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="50c8d-277">Página de índice</span><span class="sxs-lookup"><span data-stu-id="50c8d-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="50c8d-278">Componente App</span><span class="sxs-lookup"><span data-stu-id="50c8d-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="50c8d-279">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="50c8d-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="50c8d-280">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="50c8d-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="50c8d-281">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="50c8d-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="50c8d-282">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="50c8d-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="50c8d-283">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="50c8d-283">Run the app</span></span>

<span data-ttu-id="50c8d-284">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="50c8d-284">Run the app from the Server project.</span></span> <span data-ttu-id="50c8d-285">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="50c8d-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="50c8d-286">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="50c8d-287">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="50c8d-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="50c8d-288">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="50c8d-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="50c8d-289">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="50c8d-289">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="50c8d-290">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="50c8d-290">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="50c8d-291">Tutorial: Registro de una aplicación en Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="50c8d-291">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="50c8d-292">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="50c8d-292">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
