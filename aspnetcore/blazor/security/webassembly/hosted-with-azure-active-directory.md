---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory
author: guardrex
description: Obtenga información sobre cómo proteger una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e65be6e2ddc1a9de6f0ba20fe50f63b650e0bff5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792041"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="61a22-103">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="61a22-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="61a22-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="61a22-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="61a22-105">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="61a22-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="61a22-106">En las aplicaciones Blazor WebAssembly creadas en Visual Studio que se han configurado para admitir cuentas de un directorio organizativo de AAD, Visual Studio no configura actualmente los proyectos de la solución o los registros de aplicaciones de Azure Portal correctamente durante la generación del proyecto.</span><span class="sxs-lookup"><span data-stu-id="61a22-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="61a22-107">Esto se va a corregir en una próxima versión de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61a22-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="61a22-108">En este artículo se muestra cómo crear la solución y los registros del portal de aplicaciones de Azure con el comando de la CLI de .NET `dotnet new` y mediante la creación manual de los registros de aplicaciones en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="61a22-108">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="61a22-109">Si prefiere crear la solución y los registros de aplicaciones de Azure con Visual Studio antes de que se actualice el IDE, consulte **_cada sección de este artículo_** y confirme o actualice las configuraciones de las aplicaciones y los registros de aplicaciones después de que Visual Studio cree la solución.</span><span class="sxs-lookup"><span data-stu-id="61a22-109">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="61a22-110">Registro de aplicaciones en AAD y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="61a22-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="61a22-111">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="61a22-111">Create a tenant</span></span>

<span data-ttu-id="61a22-112">Siga las instrucciones que encontrará en [Inicio rápido: Configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="61a22-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="61a22-113">Registro de una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="61a22-113">Register a server API app</span></span>

<span data-ttu-id="61a22-114">Registre una aplicación de AAD para la *aplicación de API de servidor*:</span><span class="sxs-lookup"><span data-stu-id="61a22-114">Register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="61a22-115">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="61a22-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="61a22-116">Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="61a22-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="61a22-117">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="61a22-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="61a22-118">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="61a22-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="61a22-119">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="61a22-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="61a22-120">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="61a22-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="61a22-121">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-121">Select **Register**.</span></span>

<span data-ttu-id="61a22-122">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="61a22-122">Record the following information:</span></span>

* <span data-ttu-id="61a22-123">Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="61a22-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="61a22-124">Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.</span><span class="sxs-lookup"><span data-stu-id="61a22-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="61a22-125">Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="61a22-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="61a22-126">En **Permisos de API**, quite el permiso **Microsoft Graph** > **User.Read**, ya que la aplicación no requiere inicio de sesión ni acceso de perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="61a22-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="61a22-127">En **Exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="61a22-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="61a22-128">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="61a22-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="61a22-129">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="61a22-130">Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="61a22-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="61a22-131">Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="61a22-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="61a22-132">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="61a22-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="61a22-133">Confirme que **Estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="61a22-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="61a22-134">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="61a22-134">Select **Add scope**.</span></span>

<span data-ttu-id="61a22-135">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="61a22-135">Record the following information:</span></span>

* <span data-ttu-id="61a22-136">URI del identificador de aplicación (por ejemplo, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)</span><span class="sxs-lookup"><span data-stu-id="61a22-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="61a22-137">Nombre del ámbito (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="61a22-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="61a22-138">Registrar una aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="61a22-138">Register a client app</span></span>

<span data-ttu-id="61a22-139">Registre una aplicación de AAD para la *aplicación cliente*:</span><span class="sxs-lookup"><span data-stu-id="61a22-139">Register an AAD app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="61a22-140">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="61a22-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="61a22-141">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="61a22-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="61a22-142">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="61a22-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="61a22-143">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="61a22-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="61a22-144">Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="61a22-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="61a22-145">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="61a22-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="61a22-146">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61a22-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="61a22-147">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="61a22-148">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="61a22-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="61a22-149">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="61a22-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="61a22-150">Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="61a22-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="61a22-151">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-151">Select **Register**.</span></span>

<span data-ttu-id="61a22-152">Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="61a22-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="61a22-153">En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única**:</span><span class="sxs-lookup"><span data-stu-id="61a22-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="61a22-154">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="61a22-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="61a22-155">En **Concesión implícita**, asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="61a22-155">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="61a22-156">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="61a22-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="61a22-157">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="61a22-158">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="61a22-158">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="61a22-159">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="61a22-159">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="61a22-160">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="61a22-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="61a22-161">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="61a22-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="61a22-162">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="61a22-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="61a22-163">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="61a22-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="61a22-164">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61a22-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="61a22-165">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="61a22-166">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="61a22-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="61a22-167">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="61a22-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="61a22-168">Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="61a22-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="61a22-169">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-169">Select **Register**.</span></span>

<span data-ttu-id="61a22-170">Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="61a22-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="61a22-171">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="61a22-171">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="61a22-172">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="61a22-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="61a22-173">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="61a22-173">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="61a22-174">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="61a22-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="61a22-175">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="61a22-176">En **Permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="61a22-176">In **API permissions**:</span></span>

1. <span data-ttu-id="61a22-177">Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read**.</span><span class="sxs-lookup"><span data-stu-id="61a22-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="61a22-178">Seleccione **Agregar un permiso**, seguido de **Mis API**.</span><span class="sxs-lookup"><span data-stu-id="61a22-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="61a22-179">Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="61a22-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="61a22-180">Abra la lista **API**.</span><span class="sxs-lookup"><span data-stu-id="61a22-180">Open the **API** list.</span></span>
1. <span data-ttu-id="61a22-181">Habilite el acceso a la API (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="61a22-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="61a22-182">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="61a22-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="61a22-183">Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** .</span><span class="sxs-lookup"><span data-stu-id="61a22-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="61a22-184">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="61a22-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="61a22-185">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="61a22-185">Create the app</span></span>

<span data-ttu-id="61a22-186">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="61a22-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="61a22-187">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="61a22-187">Placeholder</span></span>                  | <span data-ttu-id="61a22-188">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="61a22-188">Azure portal name</span></span>                                     | <span data-ttu-id="61a22-189">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="61a22-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="61a22-190">Identificador de aplicación (cliente) para la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="61a22-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="61a22-191">Nombre de ámbito</span><span class="sxs-lookup"><span data-stu-id="61a22-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="61a22-192">Identificador de aplicación (cliente) para la *aplicación de API de servidor*</span><span class="sxs-lookup"><span data-stu-id="61a22-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="61a22-193">URI de Id. de aplicación&dagger;</span><span class="sxs-lookup"><span data-stu-id="61a22-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="61a22-194">Dominio Principal/Publicador/Inquilino</span><span class="sxs-lookup"><span data-stu-id="61a22-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="61a22-195">Id. de directorio (inquilino)</span><span class="sxs-lookup"><span data-stu-id="61a22-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="61a22-196">&dagger;La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="61a22-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="61a22-197">Al proporcionar el URI de identificador de aplicación para el marcador de posición `{SERVER API APP ID URI}` y si el esquema es `api://`, quite el esquema (`api://`) del argumento, como se muestra en el valor de ejemplo de la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="61a22-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="61a22-198">Si el URI de identificador de aplicación es un valor personalizado o tiene algún otro esquema (por ejemplo, `https://` para un dominio de publicador que no es de confianza similar a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), debe actualizar manualmente el URI del ámbito predeterminado y quitar el esquema `api://` después de que la plantilla cree la aplicación *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="61a22-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="61a22-199">Para obtener más información, consulte la nota de la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="61a22-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="61a22-200">La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="61a22-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="61a22-201">Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="61a22-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="61a22-202">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61a22-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="61a22-203">Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Configuración de la aplicación](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="61a22-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="61a22-204">Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="61a22-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="61a22-205">En Azure Portal, el valor de configuración de plataforma de la aplicación *`Client`* **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="61a22-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="61a22-206">Si la aplicación *`Client`* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="61a22-207">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación *`Client`* , vuelva al registro de la aplicación *`Client`* en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="61a22-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="61a22-208">Configuración de la aplicación *`Server`*</span><span class="sxs-lookup"><span data-stu-id="61a22-208">*`Server`* app configuration</span></span>

<span data-ttu-id="61a22-209">*Esta sección atañe a la aplicación **`Server`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="61a22-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="61a22-210">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="61a22-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="61a22-211">La compatibilidad con la autenticación y autorización de llamadas realizadas a las API web de ASP.NET Core con Microsoft Identity Platform se proporciona mediante los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="61a22-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="61a22-212">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="61a22-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="61a22-213">La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI):</span><span class="sxs-lookup"><span data-stu-id="61a22-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="61a22-214">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="61a22-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="61a22-215">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="61a22-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="61a22-216">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61a22-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="61a22-217">El método <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> configura servicios para proteger la API web con Microsoft Identity Platform v2.0.</span><span class="sxs-lookup"><span data-stu-id="61a22-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="61a22-218">Este método espera una sección `AzureAd` en la configuración de la aplicación con los ajustes necesarios para inicializar las opciones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="61a22-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="61a22-219">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61a22-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="61a22-220">El método <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="61a22-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="61a22-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="61a22-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="61a22-222">La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="61a22-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="61a22-223">Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="61a22-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="61a22-224">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="61a22-224">User.Identity.Name</span></span>

<span data-ttu-id="61a22-225">De manera predeterminada, la API de la aplicación *`Server`* rellena `User.Identity.Name` con el valor del tipo de notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="61a22-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="61a22-226">Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`:</span><span class="sxs-lookup"><span data-stu-id="61a22-226">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="61a22-227">Agregue un espacio de nombres de <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> a `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="61a22-227">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="61a22-228">Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="61a22-228">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="61a22-229">Configure <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="61a22-229">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="61a22-230">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="61a22-230">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="61a22-231">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="61a22-231">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="61a22-232">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="61a22-232">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="61a22-233">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="61a22-233">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="61a22-234">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="61a22-234">Example:</span></span>

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

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="61a22-235">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="61a22-235">WeatherForecast controller</span></span>

<span data-ttu-id="61a22-236">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="61a22-236">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="61a22-237">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="61a22-237">It's **important** to understand that:</span></span>

* <span data-ttu-id="61a22-238">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.</span><span class="sxs-lookup"><span data-stu-id="61a22-238">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="61a22-239">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="61a22-239">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="61a22-240">Configuración de la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="61a22-240">*`Client`* app configuration</span></span>

<span data-ttu-id="61a22-241">*Esta sección atañe a la aplicación **`Client`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="61a22-241">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="61a22-242">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="61a22-242">Authentication package</span></span>

<span data-ttu-id="61a22-243">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="61a22-243">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="61a22-244">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="61a22-244">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="61a22-245">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="61a22-245">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="61a22-246">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="61a22-246">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="61a22-247">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="61a22-247">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="61a22-248">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="61a22-248">Authentication service support</span></span>

<span data-ttu-id="61a22-249">Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="61a22-249">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="61a22-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="61a22-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="61a22-251">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="61a22-251">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="61a22-252">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="61a22-252">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="61a22-253">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="61a22-253">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="61a22-254">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="61a22-254">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="61a22-255">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="61a22-255">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="61a22-256">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61a22-256">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="61a22-257">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="61a22-257">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="61a22-258">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="61a22-258">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="61a22-259">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="61a22-259">Access token scopes</span></span>

<span data-ttu-id="61a22-260">Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="61a22-260">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="61a22-261">Están incluidos de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="61a22-261">Included by default in the sign in request.</span></span>
* <span data-ttu-id="61a22-262">Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="61a22-262">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="61a22-263">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="61a22-263">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="61a22-264">En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:</span><span class="sxs-lookup"><span data-stu-id="61a22-264">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="61a22-265">La plantilla Blazor WebAssembly agrega automáticamente un esquema `api://` al argumento de URI del identificador de aplicación que se pasa en el comando `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="61a22-265">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="61a22-266">Al generar una aplicación a partir de la plantilla de proyecto Blazor, confirme que el valor del ámbito de token de acceso predeterminado usa el valor de URI de identificador de aplicación personalizado correcto que proporcionó en Azure Portal o un valor con **uno** de los siguientes formatos:</span><span class="sxs-lookup"><span data-stu-id="61a22-266">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="61a22-267">Cuando el dominio del publicador del directorio es **de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:</span><span class="sxs-lookup"><span data-stu-id="61a22-267">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="61a22-268">Inspeccione el valor de un esquema doble (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="61a22-268">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="61a22-269">Si hay un esquema doble, quite el primer esquema `api://` del valor.</span><span class="sxs-lookup"><span data-stu-id="61a22-269">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="61a22-270">Cuando el dominio del publicador del directorio **no es de confianza**, el ámbito del token de acceso predeterminado suele ser un valor similar al del ejemplo siguiente, donde `API.Access` es el nombre del ámbito predeterminado:</span><span class="sxs-lookup"><span data-stu-id="61a22-270">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="61a22-271">Inspeccione el valor para ver si hay un esquema `api://` adicional (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="61a22-271">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="61a22-272">Si hay un esquema `api://` adicional, quite el esquema `api://` del valor.</span><span class="sxs-lookup"><span data-stu-id="61a22-272">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="61a22-273">La plantilla Blazor WebAssembly se puede cambiar en una versión futura de ASP.NET Core para abordar estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="61a22-273">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="61a22-274">Para obtener más información, consulte [Esquema doble para el URI de identificador de aplicación con la plantilla WASM Blazor (hospedada, una sola organización) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="61a22-274">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="61a22-275">Especifique ámbitos adicionales con `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="61a22-275">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="61a22-276">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="61a22-276">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="61a22-277">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="61a22-277">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="61a22-278">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="61a22-278">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="61a22-279">Modo de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="61a22-279">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="61a22-280">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="61a22-280">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="61a22-281">Página de índice</span><span class="sxs-lookup"><span data-stu-id="61a22-281">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="61a22-282">Componente App</span><span class="sxs-lookup"><span data-stu-id="61a22-282">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="61a22-283">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="61a22-283">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="61a22-284">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="61a22-284">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="61a22-285">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="61a22-285">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="61a22-286">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="61a22-286">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="61a22-287">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="61a22-287">Run the app</span></span>

<span data-ttu-id="61a22-288">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="61a22-288">Run the app from the Server project.</span></span> <span data-ttu-id="61a22-289">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="61a22-289">When using Visual Studio, either:</span></span>

* <span data-ttu-id="61a22-290">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-290">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="61a22-291">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="61a22-291">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="61a22-292">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="61a22-292">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="61a22-293">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="61a22-293">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="61a22-294">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="61a22-294">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
* [<span data-ttu-id="61a22-295">Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="61a22-295">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app)
