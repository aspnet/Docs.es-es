---
title: Autenticación en la nube con Azure Active Directory B2C en ASP.NET Core
author: camsoper
description: Descubra cómo configurar la autenticación de Azure Active Directory B2C con ASP.NET Core.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: a1bac04944d9671df3f804a9724c9f0fd6f1fb27
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633635"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="5a3e1-103">Autenticación en la nube con Azure Active Directory B2C en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a3e1-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="5a3e1-104">Por [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="5a3e1-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="5a3e1-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) es una solución de administración de identidades en la nube para aplicaciones web y móviles.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="5a3e1-106">El servicio proporciona autenticación para las aplicaciones hospedadas en la nube y en el entorno local.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="5a3e1-107">Los tipos de autenticación incluyen cuentas individuales, cuentas de redes sociales y cuentas empresariales federadas.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="5a3e1-108">Además, Azure AD B2C puede proporcionar autenticación multifactor con la configuración mínima.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="5a3e1-109">Azure Active Directory (Azure AD) y Azure AD B2C son ofertas de productos independientes.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="5a3e1-110">Un inquilino de Azure AD representa una organización, mientras que un inquilino de Azure AD B2C representa una colección de identidades que se van a usar con las aplicaciones de usuario de confianza.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="5a3e1-111">Para obtener más información, consulte [Azure ad B2C: preguntas más frecuentes (p + f)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="5a3e1-112">En este tutorial, aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a3e1-113">Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="5a3e1-114">Registrar una aplicación en Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="5a3e1-115">Usar Visual Studio para crear una aplicación Web de ASP.NET Core configurada para usar el inquilino de Azure AD B2C para la autenticación</span><span class="sxs-lookup"><span data-stu-id="5a3e1-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="5a3e1-116">Configuración de directivas que controlan el comportamiento del inquilino de Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a3e1-117">Prerrequisitos</span><span class="sxs-lookup"><span data-stu-id="5a3e1-117">Prerequisites</span></span>

<span data-ttu-id="5a3e1-118">Para este tutorial se requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="5a3e1-119">Microsoft Azure suscripción</span><span class="sxs-lookup"><span data-stu-id="5a3e1-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="5a3e1-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="5a3e1-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="5a3e1-121">Crear el inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="5a3e1-122">Cree un inquilino de Azure Active Directory B2C [como se describe en la documentación de](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="5a3e1-123">Cuando se le solicite, la Asociación del inquilino con una suscripción de Azure es opcional para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="5a3e1-124">Registrar la aplicación en Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="5a3e1-125">En el inquilino de Azure AD B2C recién creado, registre la aplicación con [los pasos de la documentación](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) en la sección **registrar una aplicación web** .</span><span class="sxs-lookup"><span data-stu-id="5a3e1-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="5a3e1-126">Detenga en la sección **creación de un secreto de cliente de aplicación web** .</span><span class="sxs-lookup"><span data-stu-id="5a3e1-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="5a3e1-127">No se requiere un secreto de cliente para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="5a3e1-128">Use los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-128">Use the following values:</span></span>

| <span data-ttu-id="5a3e1-129">Configuración</span><span class="sxs-lookup"><span data-stu-id="5a3e1-129">Setting</span></span>                       | <span data-ttu-id="5a3e1-130">Value</span><span class="sxs-lookup"><span data-stu-id="5a3e1-130">Value</span></span>                     | <span data-ttu-id="5a3e1-131">Notas</span><span class="sxs-lookup"><span data-stu-id="5a3e1-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="5a3e1-132">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-132">**Name**</span></span>                      | <span data-ttu-id="5a3e1-133">*&lt;nombre de la aplicación&gt;*</span><span class="sxs-lookup"><span data-stu-id="5a3e1-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="5a3e1-134">Escriba un **nombre** para la aplicación que describa la aplicación a los consumidores.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="5a3e1-135">**Incluir aplicación web o API web**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-135">**Include web app / web API**</span></span> | <span data-ttu-id="5a3e1-136">Sí</span><span class="sxs-lookup"><span data-stu-id="5a3e1-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="5a3e1-137">**Permitir flujo implícito**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="5a3e1-138">Sí</span><span class="sxs-lookup"><span data-stu-id="5a3e1-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="5a3e1-139">**URL de respuesta**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="5a3e1-140">Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="5a3e1-141">Visual Studio proporciona la dirección URL de respuesta que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="5a3e1-142">Por ahora, escriba `https://localhost:44300/signin-oidc` para completar el formulario.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="5a3e1-143">**URI de id. de aplicación**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-143">**App ID URI**</span></span>                | <span data-ttu-id="5a3e1-144">Déjelo en blanco.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-144">Leave blank</span></span>               | <span data-ttu-id="5a3e1-145">No es necesario para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="5a3e1-146">**Incluir cliente nativo**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-146">**Include native client**</span></span>     | <span data-ttu-id="5a3e1-147">No</span><span class="sxs-lookup"><span data-stu-id="5a3e1-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="5a3e1-148">Si va a configurar una dirección URL de respuesta que no sea localhost, tenga en cuenta las [restricciones en lo que se permite en la lista de direcciones URL de respuesta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="5a3e1-149">Una vez registrada la aplicación, se muestra la lista de aplicaciones del inquilino.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="5a3e1-150">Seleccione la aplicación que acaba de registrar.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-150">Select the app that was just registered.</span></span> <span data-ttu-id="5a3e1-151">Seleccione el icono de **copia** situado a la derecha del campo ID. de **aplicación** para copiarlo en el portapapeles.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="5a3e1-152">En este momento, no se puede configurar nada más en el inquilino de Azure AD B2C, pero deje abierta la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="5a3e1-153">Después de crear la aplicación ASP.NET Core se crea una configuración.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="5a3e1-154">Creación de una aplicación de ASP.NET Core en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a3e1-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="5a3e1-155">La plantilla aplicación Web de Visual Studio se puede configurar para usar el inquilino de Azure AD B2C para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="5a3e1-156">En Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-156">In Visual Studio:</span></span>

1. <span data-ttu-id="5a3e1-157">Cree una aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="5a3e1-158">Seleccione **aplicación web** en la lista de plantillas.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="5a3e1-159">Seleccione el botón **cambiar autenticación** .</span><span class="sxs-lookup"><span data-stu-id="5a3e1-159">Select the **Change Authentication** button.</span></span>
    
    ![Botón cambiar autenticación](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="5a3e1-161">En el cuadro de diálogo **cambiar autenticación** , seleccione **cuentas de usuario individuales**y, a continuación, seleccione **conectarse a un almacén de usuario existente en la nube** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Cuadro de diálogo cambiar autenticación](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="5a3e1-163">Complete el formulario con los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="5a3e1-164">Configuración</span><span class="sxs-lookup"><span data-stu-id="5a3e1-164">Setting</span></span>                       | <span data-ttu-id="5a3e1-165">Value</span><span class="sxs-lookup"><span data-stu-id="5a3e1-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="5a3e1-166">**Nombre de dominio**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-166">**Domain Name**</span></span>               | <span data-ttu-id="5a3e1-167">*&lt;el nombre de dominio del inquilino de B2C.&gt;*</span><span class="sxs-lookup"><span data-stu-id="5a3e1-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="5a3e1-168">**Identificador de la aplicación**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-168">**Application ID**</span></span>            | <span data-ttu-id="5a3e1-169">*&lt;pegar el identificador de la aplicación del portapapeles&gt;*</span><span class="sxs-lookup"><span data-stu-id="5a3e1-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="5a3e1-170">**Ruta de devolución de llamada**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-170">**Callback Path**</span></span>             | <span data-ttu-id="5a3e1-171">*&lt;usar el valor predeterminado&gt;*</span><span class="sxs-lookup"><span data-stu-id="5a3e1-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="5a3e1-172">**Directiva de registro o de inicio de sesión**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="5a3e1-173">**Restablecer Directiva de contraseñas**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="5a3e1-174">**Editar Directiva de perfil**</span><span class="sxs-lookup"><span data-stu-id="5a3e1-174">**Edit profile policy**</span></span>       | <span data-ttu-id="5a3e1-175">*&lt;dejar en blanco&gt;*</span><span class="sxs-lookup"><span data-stu-id="5a3e1-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="5a3e1-176">Seleccione el vínculo **copiar** junto a **URI de respuesta** para copiar el URI de respuesta en el portapapeles.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="5a3e1-177">Seleccione **Aceptar** para cerrar el cuadro de diálogo **cambiar autenticación** .</span><span class="sxs-lookup"><span data-stu-id="5a3e1-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="5a3e1-178">Seleccione **Aceptar** para crear la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="5a3e1-179">Finalizar el registro de la aplicación B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-179">Finish the B2C app registration</span></span>

<span data-ttu-id="5a3e1-180">Vuelva a la ventana del explorador con las propiedades de la aplicación B2C todavía abiertas.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="5a3e1-181">Cambie la **dirección URL de respuesta** temporal especificada anteriormente al valor copiado desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="5a3e1-182">Seleccione **Guardar** en la parte superior de la ventana.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="5a3e1-183">Si no ha copiado la dirección URL de respuesta, use la dirección HTTPS de la pestaña depurar en las propiedades del proyecto web y Anexe el valor **CallbackPath** de *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="5a3e1-184">Configurar directivas</span><span class="sxs-lookup"><span data-stu-id="5a3e1-184">Configure policies</span></span>

<span data-ttu-id="5a3e1-185">Siga los pasos de la documentación de Azure AD B2C para [crear una directiva de registro o de inicio de sesión](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)y, a continuación, [cree una directiva de restablecimiento de contraseña](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="5a3e1-186">Use los valores de ejemplo proporcionados en la documentación para los \*\* Identity proveedores\*\*, **los atributos de registro**y las **notificaciones**de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="5a3e1-187">El uso del botón **Ejecutar ahora** para probar las directivas tal y como se describe en la documentación es opcional.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="5a3e1-188">Asegúrese de que los nombres de las directivas sean exactamente como se describen en la documentación, ya que esas directivas se usaron en el cuadro de diálogo **cambiar autenticación** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="5a3e1-189">Los nombres de las directivas se pueden comprobar en *appsettings.jsen*.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="5a3e1-190">Configuración de las opciones OpenIdConnectOptions/JwtBearer/presubyacentes Cookie</span><span class="sxs-lookup"><span data-stu-id="5a3e1-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="5a3e1-191">Para configurar las opciones subyacentes directamente, use la constante de esquema adecuada en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a3e1-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="5a3e1-192">Ejecución de la aplicación</span><span class="sxs-lookup"><span data-stu-id="5a3e1-192">Run the app</span></span>

<span data-ttu-id="5a3e1-193">En Visual Studio, presione **F5** para compilar y ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="5a3e1-194">Una vez iniciada la aplicación Web, seleccione **Aceptar** para aceptar el uso de cookie s (si se le solicita) y, a continuación, seleccione **iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Inicio de sesión en la aplicación](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="5a3e1-196">El explorador redirige al inquilino de Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="5a3e1-197">Inicie sesión con una cuenta existente (si se creó una prueba de las directivas) o seleccione **registrarse ahora** para crear una cuenta nueva.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="5a3e1-198">El vínculo **¿olvidó su contraseña?** se usa para restablecer una contraseña olvidada.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Inicio de sesión de Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="5a3e1-200">Después de iniciar sesión correctamente, el explorador se redirige a la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Correcto](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="5a3e1-202">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="5a3e1-202">Next steps</span></span>

<span data-ttu-id="5a3e1-203">En este tutorial, ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a3e1-204">Creación de un inquilino de Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="5a3e1-205">Registrar una aplicación en Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="5a3e1-206">Usar Visual Studio para crear una aplicación Web de ASP.NET Core configurada para usar el inquilino de Azure AD B2C para la autenticación</span><span class="sxs-lookup"><span data-stu-id="5a3e1-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="5a3e1-207">Configuración de directivas que controlan el comportamiento del inquilino de Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="5a3e1-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="5a3e1-208">Ahora que la aplicación ASP.NET Core está configurada para usar Azure AD B2C para la autenticación, se puede usar el [atributo Authorize](xref:security/authorization/simple) para proteger la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="5a3e1-209">Siga desarrollando su aplicación aprendiendo a:</span><span class="sxs-lookup"><span data-stu-id="5a3e1-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="5a3e1-210">[Personalización de la interfaz de usuario de Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="5a3e1-211">[Configurar los requisitos de complejidad de la contraseña](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="5a3e1-212">[Habilitar multi-factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="5a3e1-213">Configure proveedores de identidades adicionales, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)y otros.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="5a3e1-214">[Use el Graph API Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar información de usuario adicional, como la pertenencia a grupos, del inquilino de Azure ad B2C.</span><span class="sxs-lookup"><span data-stu-id="5a3e1-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="5a3e1-215">[Protección de una API web ASP.net Core mediante Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="5a3e1-216">[Tutorial: conceder acceso a una API Web de ASP.net mediante Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="5a3e1-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
