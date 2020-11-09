---
title: 'Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory'
author: guardrex
description: 'Obtenga información sobre cómo proteger una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: cb1deb71723660964954c2faae4512b7df9b2ed4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690543"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="f3dc7-103">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="f3dc7-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="f3dc7-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f3dc7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f3dc7-105">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f3dc7-106">En las aplicaciones de Blazor WebAssembly creadas en Visual Studio que se han configurado para admitir cuentas de un directorio organizativo de AAD, Visual Studio no configura la aplicación correctamente durante la generación del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="f3dc7-107">Esto se va a corregir en una próxima versión de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="f3dc7-108">En este artículo se muestra cómo crear la aplicación con el comando `dotnet new` de la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="f3dc7-109">Si prefiere crear la aplicación con Visual Studio antes de la actualización del IDE con las plantillas más recientes de Blazor de ASP.NET Core 5.0, vea cada sección de este artículo y confirme o actualice la configuración de la aplicación después de que Visual Studio cree la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="f3dc7-110">Registro de aplicaciones en AAD y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="f3dc7-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="f3dc7-111">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="f3dc7-111">Create a tenant</span></span>

<span data-ttu-id="f3dc7-112">Siga las instrucciones que encontrará en [Inicio rápido: Configuración de un inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para crear un inquilino en AAD.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="f3dc7-113">Registro de una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="f3dc7-113">Register a server API app</span></span>

<span data-ttu-id="f3dc7-114">Siga las instrucciones que encontrará en [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app) y en temas de Azure AAD posteriores para registrar una aplicación de AAD para la *aplicación de API de servidor*. Tras ello, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="f3dc7-115">En **Azure Active Directory** > **Registros de aplicaciones** , seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-115">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="f3dc7-116">Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-116">Provide a **Name** for the app (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="f3dc7-117">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="f3dc7-118">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f3dc7-119">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="f3dc7-120">Desactive la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f3dc7-121">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-121">Select **Register**.</span></span>

<span data-ttu-id="f3dc7-122">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-122">Record the following information:</span></span>

* <span data-ttu-id="f3dc7-123">Identificador de aplicación (cliente) de la *aplicación de API de servidor* ; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="f3dc7-124">Identificador de directorio (inquilino); por ejemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="f3dc7-125">Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="f3dc7-126">En **Permisos de API** , quite el permiso **Microsoft Graph** > **User.Read** , ya que la aplicación no requiere inicio de sesión ni acceso de perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="f3dc7-127">En **Exponer una API** :</span><span class="sxs-lookup"><span data-stu-id="f3dc7-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="f3dc7-128">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="f3dc7-129">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="f3dc7-130">Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f3dc7-131">Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="f3dc7-132">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="f3dc7-133">Confirme que **Estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="f3dc7-134">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-134">Select **Add scope**.</span></span>

<span data-ttu-id="f3dc7-135">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-135">Record the following information:</span></span>

* <span data-ttu-id="f3dc7-136">URI del identificador de aplicación (por ejemplo, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)</span><span class="sxs-lookup"><span data-stu-id="f3dc7-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="f3dc7-137">Nombre del ámbito (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="f3dc7-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="f3dc7-138">Registrar una aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="f3dc7-138">Register a client app</span></span>

<span data-ttu-id="f3dc7-139">Siga las instrucciones que encontrará en [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-register-app) y en temas de Azure AAD posteriores para registrar una aplicación de AAD para la aplicación *`Client`* . Tras ello, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f3dc7-140">En **Azure Active Directory** > **Registros de aplicaciones** , seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-140">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="f3dc7-141">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-141">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="f3dc7-142">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="f3dc7-143">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f3dc7-144">Establezca la lista desplegable **URI de redirección** en **Aplicación de página única** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f3dc7-145">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f3dc7-146">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f3dc7-147">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f3dc7-148">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f3dc7-149">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f3dc7-150">Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f3dc7-151">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-151">Select **Register**.</span></span>

<span data-ttu-id="f3dc7-152">Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="f3dc7-153">En **Autenticación** > **Configuraciones de plataforma** > **Aplicación de página única** :</span><span class="sxs-lookup"><span data-stu-id="f3dc7-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="f3dc7-154">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f3dc7-155">En **Concesión implícita** , asegúrese de que las casillas **Tokens de acceso** y **Tokens de id.** **no** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="f3dc7-156">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f3dc7-157">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f3dc7-158">En **Azure Active Directory** > **Registros de aplicaciones** , seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-158">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="f3dc7-159">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-159">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="f3dc7-160">Elija un **tipo de cuenta compatible**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="f3dc7-161">En esta experiencia puede seleccionar **Solo cuentas de este directorio organizativo** (inquilino único).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f3dc7-162">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f3dc7-163">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f3dc7-164">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f3dc7-165">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación *`Server`* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f3dc7-166">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f3dc7-167">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f3dc7-168">Desactive la casilla **Permisos**>**Conceda consentimiento del administrador a los permisos openid y offline_access**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f3dc7-169">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-169">Select **Register**.</span></span>

<span data-ttu-id="f3dc7-170">Registre el identificador de la aplicación *`Client`* (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="f3dc7-171">En **Autenticación** > **Configuraciones de plataforma** > **Web** :</span><span class="sxs-lookup"><span data-stu-id="f3dc7-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="f3dc7-172">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f3dc7-173">En **Concesión implícita** , active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="f3dc7-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f3dc7-174">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f3dc7-175">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="f3dc7-176">En **Permisos de API** :</span><span class="sxs-lookup"><span data-stu-id="f3dc7-176">In **API permissions** :</span></span>

1. <span data-ttu-id="f3dc7-177">Confirme que la aplicación tiene el permiso **Microsoft Graph** > **User.Read**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="f3dc7-178">Seleccione **Agregar un permiso** , seguido de **Mis API**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="f3dc7-179">Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="f3dc7-180">Abra la lista **API**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-180">Open the **API** list.</span></span>
1. <span data-ttu-id="f3dc7-181">Habilite el acceso a la API (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f3dc7-182">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="f3dc7-183">Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** .</span><span class="sxs-lookup"><span data-stu-id="f3dc7-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="f3dc7-184">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="f3dc7-185">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-185">Create the app</span></span>

<span data-ttu-id="f3dc7-186">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="f3dc7-187">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="f3dc7-187">Placeholder</span></span>                  | <span data-ttu-id="f3dc7-188">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="f3dc7-188">Azure portal name</span></span>                                     | <span data-ttu-id="f3dc7-189">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="f3dc7-189">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="f3dc7-190">Identificador de aplicación (cliente) para la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="f3dc7-191">Nombre de ámbito</span><span class="sxs-lookup"><span data-stu-id="f3dc7-191">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="f3dc7-192">Identificador de aplicación (cliente) para la *aplicación de API de servidor*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="f3dc7-193">URI de Id. de aplicación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-193">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="f3dc7-194">Dominio Principal/Publicador/Inquilino</span><span class="sxs-lookup"><span data-stu-id="f3dc7-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="f3dc7-195">Id. de directorio (inquilino)</span><span class="sxs-lookup"><span data-stu-id="f3dc7-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="f3dc7-196">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-196">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f3dc7-197">Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Configuración de la aplicación](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-197">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f3dc7-198">Es posible que se requiera un cambio de configuración al usar un inquilino de Azure con un dominio de publicador no comprobado, lo cual se describe en la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-198">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f3dc7-199">En Azure Portal, el valor de configuración de plataforma de la aplicación *`Client`* **URI de redirección** de la aplicación se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-199">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="f3dc7-200">Si la aplicación *`Client`* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor* , en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-200">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="f3dc7-201">Si el puerto no se configuró anteriormente con el puerto conocido de la aplicación *`Client`* , vuelva al registro de la aplicación *`Client`* en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-201">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f3dc7-202">Configuración de la aplicación *`Server`*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-202">*`Server`* app configuration</span></span>

<span data-ttu-id="f3dc7-203">*Esta sección atañe a la aplicación **`Server`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-203">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f3dc7-204">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-204">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f3dc7-205">La compatibilidad con la autenticación y autorización de llamadas realizadas a las API web de ASP.NET Core con Microsoft Identity Platform se proporciona mediante los paquetes siguientes:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-205">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="f3dc7-206">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-206">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f3dc7-207">La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI):</span><span class="sxs-lookup"><span data-stu-id="f3dc7-207">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="f3dc7-208">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="f3dc7-209">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-209">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f3dc7-210">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-210">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f3dc7-211">El método <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> configura servicios para proteger la API web con Microsoft Identity Platform v2.0.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-211">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="f3dc7-212">Este método espera una sección `AzureAd` en la configuración de la aplicación con los ajustes necesarios para inicializar las opciones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-212">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f3dc7-213">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-213">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f3dc7-214">El método <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-214">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="f3dc7-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="f3dc7-216">La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-216">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="f3dc7-217">Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-217">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="f3dc7-218">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="f3dc7-218">User.Identity.Name</span></span>

<span data-ttu-id="f3dc7-219">De manera predeterminada, la API de la aplicación *`Server`* rellena `User.Identity.Name` con el valor del tipo de notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (por ejemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-219">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="f3dc7-220">Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`, establezca <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-220">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="f3dc7-221">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-221">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f3dc7-222">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-222">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f3dc7-223">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-223">Example:</span></span>

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

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f3dc7-224">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-224">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f3dc7-225">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-225">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="f3dc7-226">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="f3dc7-226">WeatherForecast controller</span></span>

<span data-ttu-id="f3dc7-227">El controlador WeatherForecast ( *Controllers/WeatherForecastController.cs* ) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-227">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="f3dc7-228">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-228">It's **important** to understand that:</span></span>

* <span data-ttu-id="f3dc7-229">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-229">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="f3dc7-230">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-230">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="f3dc7-231">Configuración de la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-231">*`Client`* app configuration</span></span>

<span data-ttu-id="f3dc7-232">*Esta sección atañe a la aplicación **`Client`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="f3dc7-232">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f3dc7-233">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-233">Authentication package</span></span>

<span data-ttu-id="f3dc7-234">Cuando una aplicación se crea para usar cuentas profesionales o educativas (`SingleOrg`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-234">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="f3dc7-235">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-235">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f3dc7-236">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-236">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="f3dc7-237">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-237">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="f3dc7-238">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-238">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="f3dc7-239">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-239">Authentication service support</span></span>

<span data-ttu-id="f3dc7-240">Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-240">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f3dc7-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="f3dc7-242">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-242">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="f3dc7-243">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="f3dc7-243">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="f3dc7-244">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-244">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f3dc7-245">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-245">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f3dc7-246">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-246">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f3dc7-247">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-247">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="f3dc7-248">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-248">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="f3dc7-249">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-249">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="f3dc7-250">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="f3dc7-250">Access token scopes</span></span>

<span data-ttu-id="f3dc7-251">Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-251">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="f3dc7-252">Están incluidos de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-252">Included by default in the sign in request.</span></span>
* <span data-ttu-id="f3dc7-253">Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-253">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="f3dc7-254">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-254">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="f3dc7-255">En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-255">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f3dc7-256">Especifique ámbitos adicionales con `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-256">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="f3dc7-257">Para más información, vea las siguientes secciones del artículo *Otros escenarios* :</span><span class="sxs-lookup"><span data-stu-id="f3dc7-257">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f3dc7-258">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="f3dc7-258">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f3dc7-259">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="f3dc7-259">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="f3dc7-260">Modo de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="f3dc7-260">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="f3dc7-261">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="f3dc7-261">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f3dc7-262">Página de índice</span><span class="sxs-lookup"><span data-stu-id="f3dc7-262">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="f3dc7-263">Componente App</span><span class="sxs-lookup"><span data-stu-id="f3dc7-263">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f3dc7-264">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f3dc7-264">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f3dc7-265">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f3dc7-265">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="f3dc7-266">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="f3dc7-266">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f3dc7-267">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="f3dc7-267">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f3dc7-268">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="f3dc7-268">Run the app</span></span>

<span data-ttu-id="f3dc7-269">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-269">Run the app from the Server project.</span></span> <span data-ttu-id="f3dc7-270">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f3dc7-270">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f3dc7-271">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-271">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f3dc7-272">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="f3dc7-272">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f3dc7-273">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f3dc7-273">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f3dc7-274">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="f3dc7-274">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f3dc7-275">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="f3dc7-275">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
