---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8b8cf77cde281b8ea6a09d8edaec6606383a4c04
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130462"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="df6ed-102">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="df6ed-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="df6ed-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="df6ed-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="df6ed-104">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="df6ed-105">Registro de aplicaciones en AAD B2C y creación de una solución</span><span class="sxs-lookup"><span data-stu-id="df6ed-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="df6ed-106">Creación de un inquilino</span><span class="sxs-lookup"><span data-stu-id="df6ed-106">Create a tenant</span></span>

<span data-ttu-id="df6ed-107">Siga las instrucciones que encontrará en [Tutorial: Creación de un inquilino de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para crear un inquilino de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="df6ed-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="df6ed-108">Registre la instancia de AAD B2C (por ejemplo, `https://contoso.b2clogin.com/`, barra diagonal final incluida).</span><span class="sxs-lookup"><span data-stu-id="df6ed-108">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="df6ed-109">La instancia es el esquema y el host de un registro de la aplicación de Azure B2C, que se puede encontrar abriendo la ventana **Puntos de conexión** de la página **Registros de aplicaciones** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="df6ed-109">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="df6ed-110">Registro de una aplicación de API de servidor</span><span class="sxs-lookup"><span data-stu-id="df6ed-110">Register a server API app</span></span>

<span data-ttu-id="df6ed-111">Siga las instrucciones que encontrará en [Tutorial: Registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación de API de servidor* y, después, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df6ed-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="df6ed-112">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="df6ed-113">Indique un **nombre** para la aplicación (por ejemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="df6ed-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="df6ed-114">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o proveedor de identidades. Elija esta opción para realizar la autenticación con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="df6ed-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="df6ed-115">La *aplicación de API de servidor* no requiere un **URI de redirección** en este escenario, así que deje la lista desplegable establecida en **Web** y no especifique ningún URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="df6ed-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="df6ed-116">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="df6ed-116">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="df6ed-117">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-117">Select **Register**.</span></span>

<span data-ttu-id="df6ed-118">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="df6ed-118">Record the following information:</span></span>

* <span data-ttu-id="df6ed-119">Identificador de aplicación (cliente) de la *aplicación de API de servidor*; por ejemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="df6ed-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="df6ed-120">Dominio de AAD Principal/Publicador/Inquilino (por ejemplo, `contoso.onmicrosoft.com`): El dominio está disponible como **Dominio del publicador** en la hoja **Personalización de marca** de Azure Portal de la aplicación registrada.</span><span class="sxs-lookup"><span data-stu-id="df6ed-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="df6ed-121">En **Exponer una API**:</span><span class="sxs-lookup"><span data-stu-id="df6ed-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="df6ed-122">Seleccione **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="df6ed-123">Seleccione **Guardar y continuar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="df6ed-124">Indique un **Nombre de ámbito** (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="df6ed-125">Indique un **Nombre para mostrar del consentimiento del administrador** (por ejemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="df6ed-126">Proporcione una **Descripción del consentimiento del administrador** (por ejemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="df6ed-127">Confirme que **Estado** está establecido en **Habilitado**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="df6ed-128">Seleccione la opción **Agregar un ámbito**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-128">Select **Add scope**.</span></span>

<span data-ttu-id="df6ed-129">Registre la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="df6ed-129">Record the following information:</span></span>

* <span data-ttu-id="df6ed-130">URI del identificador de aplicación (por ejemplo, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` o el valor personalizado que haya proporcionado)</span><span class="sxs-lookup"><span data-stu-id="df6ed-130">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="df6ed-131">Ámbito predeterminado (por ejemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="df6ed-131">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="df6ed-132">El URI de identificador de aplicación puede requerir una configuración especial en la aplicación cliente, que se describe en la sección [Ámbitos de token de acceso](#access-token-scopes) más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="df6ed-132">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="df6ed-133">Registro de una aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="df6ed-133">Register a client app</span></span>

<span data-ttu-id="df6ed-134">Siga de nuevo las instrucciones que encontrará en [Tutorial: Registro de una aplicación en Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar una aplicación de AAD para la *aplicación cliente* y, después, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df6ed-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="df6ed-135">En **Azure Active Directory** > **Registros de aplicaciones**, seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="df6ed-136">Indique un **Nombre** para la aplicación (por ejemplo, **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="df6ed-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="df6ed-137">En **Tipos de cuenta compatibles**, seleccione la opción multiinquilino: **Cuentas de cualquier directorio de la organización o proveedor de identidades. Elija esta opción para realizar la autenticación con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="df6ed-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="df6ed-138">Deje la lista desplegable **URI de redirección** establecida en **Web** y proporcione el siguiente URI de redirección: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="df6ed-138">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="df6ed-139">El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001.</span><span class="sxs-lookup"><span data-stu-id="df6ed-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="df6ed-140">Si la aplicación se ejecuta en otro puerto de Kestrel, use el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="df6ed-141">En el caso de IIS Express, el puerto generado aleatoriamente para la aplicación se encuentra en las propiedades de la aplicación de servidor, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-141">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="df6ed-142">Dado que la aplicación no existe en este momento y no conocemos el puerto de IIS Express, vuelva a este paso después de crear la aplicación y actualice el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="df6ed-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="df6ed-143">En la sección [Creación de la aplicación](#create-the-app) aparece un comentario para recordar a los usuarios de IIS Express que actualicen el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="df6ed-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="df6ed-144">Confirme que la casilla **Permisos** > **Conceda consentimiento del administrador a los permisos openid y offline_access** está activada.</span><span class="sxs-lookup"><span data-stu-id="df6ed-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="df6ed-145">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-145">Select **Register**.</span></span>

<span data-ttu-id="df6ed-146">Registre el identificador de aplicación (cliente); por ejemplo, `4369008b-21fa-427c-abaa-9b53bf58e538`.</span><span class="sxs-lookup"><span data-stu-id="df6ed-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="df6ed-147">En **Autenticación** > **Configuraciones de plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="df6ed-147">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="df6ed-148">Confirme que el **URI de redirección** de `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="df6ed-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="df6ed-149">En **Concesión implícita**, active las casillas **Tokens de acceso** y **Tokens de id.**</span><span class="sxs-lookup"><span data-stu-id="df6ed-149">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="df6ed-150">Los valores predeterminados restantes de la aplicación son aceptables en esta experiencia.</span><span class="sxs-lookup"><span data-stu-id="df6ed-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="df6ed-151">Seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-151">Select the **Save** button.</span></span>

<span data-ttu-id="df6ed-152">En **Permisos de API**:</span><span class="sxs-lookup"><span data-stu-id="df6ed-152">In **API permissions**:</span></span>

1. <span data-ttu-id="df6ed-153">Seleccione **Agregar un permiso**, seguido de **Mis API**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-153">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="df6ed-154">Seleccione la *aplicación de API de servidor* en la columna **Nombre** (por ejemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="df6ed-154">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="df6ed-155">Abra la lista **API**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-155">Open the **API** list.</span></span>
1. <span data-ttu-id="df6ed-156">Habilite el acceso a la API (por ejemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-156">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="df6ed-157">Seleccione **Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-157">Select **Add permissions**.</span></span>
1. <span data-ttu-id="df6ed-158">Seleccione el botón **Conceder consentimiento de administrador para {NOMBRE DE INQUILINO}** .</span><span class="sxs-lookup"><span data-stu-id="df6ed-158">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="df6ed-159">Seleccione **Sí** para confirmar la acción.</span><span class="sxs-lookup"><span data-stu-id="df6ed-159">Select **Yes** to confirm.</span></span>

<span data-ttu-id="df6ed-160">En **Inicio** > **Azure AD B2C** > **Flujos de usuario**:</span><span class="sxs-lookup"><span data-stu-id="df6ed-160">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="df6ed-161">Creación de un flujo de usuario de registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="df6ed-161">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="df6ed-162">Como mínimo, seleccione el atributo de usuario **Notificaciones de la aplicación** > **Nombre para mostrar** para rellenar `context.User.Identity.Name` en el componente `LoginDisplay` (`Shared/LoginDisplay.razor`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-162">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="df6ed-163">Registre el nombre de flujo de usuario de inicio de sesión y de registro creado para la aplicación (por ejemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="df6ed-164">Creación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="df6ed-164">Create the app</span></span>

<span data-ttu-id="df6ed-165">Reemplace los marcadores de posición del siguiente comando por la información registrada anteriormente y ejecute el comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="df6ed-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="df6ed-166">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="df6ed-166">Placeholder</span></span>                   | <span data-ttu-id="df6ed-167">Nombre de Azure Portal</span><span class="sxs-lookup"><span data-stu-id="df6ed-167">Azure portal name</span></span>                                     | <span data-ttu-id="df6ed-168">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="df6ed-168">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="df6ed-169">Instancia</span><span class="sxs-lookup"><span data-stu-id="df6ed-169">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="df6ed-170">Identificador de aplicación (cliente) para la *aplicación cliente*</span><span class="sxs-lookup"><span data-stu-id="df6ed-170">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="df6ed-171">Nombre de ámbito</span><span class="sxs-lookup"><span data-stu-id="df6ed-171">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="df6ed-172">Identificador de aplicación (cliente) para la *aplicación de API de servidor*</span><span class="sxs-lookup"><span data-stu-id="df6ed-172">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="df6ed-173">URI de identificador de aplicación ([consultar nota](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="df6ed-173">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="df6ed-174">Flujo de usuario de registro o de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="df6ed-174">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="df6ed-175">Dominio Principal/Publicador/Inquilino</span><span class="sxs-lookup"><span data-stu-id="df6ed-175">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="df6ed-176">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-176">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="df6ed-177">Pase el URI de identificador de aplicación a la opción `app-id-uri`, pero tenga en cuenta que puede que deba realizar un cambio de configuración en la aplicación cliente; esto se describe en la sección [Ámbitos de token de acceso](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="df6ed-177">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="df6ed-178">De igual modo, el ámbito configurado por la plantilla de Blazor hospedada podría tener repetido el host de URI de identificador de aplicación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-178">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="df6ed-179">Confirme que el ámbito configurado para la colección `DefaultAccessTokenScopes` es correcto en `Program.Main` (`Program.cs`) en la *aplicación cliente*.</span><span class="sxs-lookup"><span data-stu-id="df6ed-179">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="df6ed-180">En Azure Portal, el valor de **Autenticación** > **Configuraciones de plataforma** > **Web** > **URI de redirección** de la *aplicación cliente* se establece en el puerto 5001 en el caso de las aplicaciones que se ejecutan en el servidor Kestrel con la configuración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="df6ed-180">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="df6ed-181">Si la *aplicación cliente* se ejecuta en un puerto de IIS Express aleatorio, el puerto de la aplicación se encuentra en las propiedades de la *aplicación de API de servidor*, en el panel **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-181">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="df6ed-182">Si el puerto no se configuró anteriormente con el puerto conocido de la *aplicación cliente*, vuelva al registro de la *aplicación cliente* en Azure Portal y actualice el URI de redirección con el puerto correcto.</span><span class="sxs-lookup"><span data-stu-id="df6ed-182">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="df6ed-183">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="df6ed-183">Server app configuration</span></span>

<span data-ttu-id="df6ed-184">*Esta sección atañe a la aplicación **`Server`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="df6ed-184">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="df6ed-185">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="df6ed-185">Authentication package</span></span>

<span data-ttu-id="df6ed-186">La compatibilidad con la autenticación y autorización de llamadas realizadas a API web de ASP.NET Core se proporciona a través del paquete [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/):</span><span class="sxs-lookup"><span data-stu-id="df6ed-186">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="df6ed-187">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="df6ed-187">Authentication service support</span></span>

<span data-ttu-id="df6ed-188">El método `AddAuthentication` configura los servicios de autenticación dentro de la aplicación y, asimismo, configura el controlador de portador JWT como el método de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="df6ed-188">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="df6ed-189">El método <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> configura los parámetros específicos del controlador de portador JWT necesarios para validar los tokens emitidos por la instancia de Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="df6ed-189">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="df6ed-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> garantizan que sucede lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df6ed-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="df6ed-191">La aplicación intenta analizar y validar los tokens de las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="df6ed-191">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="df6ed-192">Cualquier solicitud que intente acceder a un recurso protegido sin las credenciales adecuadas no se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="df6ed-192">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="df6ed-193">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="df6ed-193">User.Identity.Name</span></span>

<span data-ttu-id="df6ed-194">`User.Identity.Name` no se rellena de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="df6ed-194">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="df6ed-195">Para configurar la aplicación con el fin de recibir el valor del tipo de notificación `name`, establezca <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="df6ed-195">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="df6ed-196">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="df6ed-196">App settings</span></span>

<span data-ttu-id="df6ed-197">El archivo `appsettings.json` contiene las opciones para configurar el controlador de portador JWT que se usa para validar los tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="df6ed-197">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="df6ed-198">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="df6ed-198">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="df6ed-199">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="df6ed-199">WeatherForecast controller</span></span>

<span data-ttu-id="df6ed-200">El controlador WeatherForecast (*Controllers/WeatherForecastController.cs*) expone una API protegida con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado al controlador.</span><span class="sxs-lookup"><span data-stu-id="df6ed-200">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="df6ed-201">Es **importante** comprender esto:</span><span class="sxs-lookup"><span data-stu-id="df6ed-201">It's **important** to understand that:</span></span>

* <span data-ttu-id="df6ed-202">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) en este controlador de API es lo único que protege a esta API de posibles accesos no autorizados.</span><span class="sxs-lookup"><span data-stu-id="df6ed-202">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="df6ed-203">El atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) que se usa en la aplicación Blazor WebAssembly solo sirve como sugerencia a la aplicación de que el usuario debe contar con autorización para que la aplicación funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="df6ed-203">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="df6ed-204">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="df6ed-204">Client app configuration</span></span>

<span data-ttu-id="df6ed-205">*Esta sección atañe a la aplicación **`Client`** de la solución.*</span><span class="sxs-lookup"><span data-stu-id="df6ed-205">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="df6ed-206">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="df6ed-206">Authentication package</span></span>

<span data-ttu-id="df6ed-207">Cuando una aplicación se crea para usar una cuenta de B2C individual (`IndividualB2C`), la aplicación recibe automáticamente una referencia de paquete de la [Biblioteca de autenticación de Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="df6ed-207">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="df6ed-208">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="df6ed-208">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="df6ed-209">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="df6ed-209">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="df6ed-210">El paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) agrega el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a la aplicación de forma transitiva.</span><span class="sxs-lookup"><span data-stu-id="df6ed-210">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="df6ed-211">Compatibilidad con el servicio de autenticación</span><span class="sxs-lookup"><span data-stu-id="df6ed-211">Authentication service support</span></span>

<span data-ttu-id="df6ed-212">Se ha agregado compatibilidad con instancias de <xref:System.Net.Http.HttpClient> que incluye tokens de acceso al realizar solicitudes al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="df6ed-212">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="df6ed-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="df6ed-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="df6ed-214">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="df6ed-214">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="df6ed-215">La compatibilidad para autenticar usuarios se registra en el contenedor de servicios con el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> proporcionado por el paquete [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="df6ed-215">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="df6ed-216">Este método configura los servicios necesarios para que la aplicación interactúe con el proveedor de Identity.</span><span class="sxs-lookup"><span data-stu-id="df6ed-216">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="df6ed-217">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="df6ed-217">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="df6ed-218">El método <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> acepta una devolución de llamada para configurar los parámetros necesarios para autenticar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-218">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="df6ed-219">Los valores necesarios para configurar la aplicación se pueden obtener de la configuración de AAD de Azure Portal al registrar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-219">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="df6ed-220">La configuración se suministra a través del archivo `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="df6ed-220">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="df6ed-221">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="df6ed-221">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="df6ed-222">Ámbitos de token de acceso</span><span class="sxs-lookup"><span data-stu-id="df6ed-222">Access token scopes</span></span>

<span data-ttu-id="df6ed-223">Los ámbitos de token de acceso predeterminados son una lista de ámbitos de token de acceso con las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="df6ed-223">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="df6ed-224">Están incluidos de forma predeterminada en la solicitud de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="df6ed-224">Included by default in the sign in request.</span></span>
* <span data-ttu-id="df6ed-225">Se usan para aprovisionar un token de acceso inmediatamente después de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="df6ed-225">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="df6ed-226">Todos los ámbitos deben pertenecer a la misma aplicación según las reglas de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="df6ed-226">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="df6ed-227">En caso necesario, se pueden agregar más ámbitos para otras aplicaciones de API adicionales:</span><span class="sxs-lookup"><span data-stu-id="df6ed-227">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="df6ed-228">Para más información, vea las siguientes secciones del artículo *Otros escenarios*:</span><span class="sxs-lookup"><span data-stu-id="df6ed-228">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="df6ed-229">Solicitar tokens de acceso adicionales</span><span class="sxs-lookup"><span data-stu-id="df6ed-229">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="df6ed-230">Adjuntar tokens a solicitudes salientes</span><span class="sxs-lookup"><span data-stu-id="df6ed-230">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="df6ed-231">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="df6ed-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="df6ed-232">Página de índice</span><span class="sxs-lookup"><span data-stu-id="df6ed-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="df6ed-233">Componente App</span><span class="sxs-lookup"><span data-stu-id="df6ed-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="df6ed-234">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="df6ed-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="df6ed-235">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="df6ed-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="df6ed-236">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="df6ed-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="df6ed-237">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="df6ed-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="df6ed-238">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="df6ed-238">Run the app</span></span>

<span data-ttu-id="df6ed-239">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="df6ed-239">Run the app from the Server project.</span></span> <span data-ttu-id="df6ed-240">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df6ed-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="df6ed-241">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="df6ed-242">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="df6ed-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="df6ed-243">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="df6ed-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="df6ed-244">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="df6ed-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="df6ed-245">Tutorial: Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="df6ed-245">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="df6ed-246">Documentación de la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="df6ed-246">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
