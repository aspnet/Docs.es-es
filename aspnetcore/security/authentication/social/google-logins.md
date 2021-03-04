---
title: Configuración de inicio de sesión externo de Google en ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra la integración de la autenticación de usuarios de cuentas de Google en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110136"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="f707e-103">Configuración de inicio de sesión externo de Google en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f707e-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="f707e-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f707e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f707e-105">En este tutorial se muestra cómo permitir que los usuarios inicien sesión con su cuenta de Google mediante el proyecto ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f707e-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="f707e-106">Creación de un proyecto y un identificador de cliente de la consola de API de Google</span><span class="sxs-lookup"><span data-stu-id="f707e-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="f707e-107">Agregue el paquete de NuGet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f707e-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="f707e-108">Siga las instrucciones de [integración de google Sign-In en la aplicación web](https://developers.google.com/identity/sign-in/web/sign-in) (documentación de Google).</span><span class="sxs-lookup"><span data-stu-id="f707e-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="f707e-109">En la página **credenciales** de la [consola de Google](https://console.developers.google.com/apis/credentials), seleccione **crear credenciales**  >  **ID**. de cliente de OAuth.</span><span class="sxs-lookup"><span data-stu-id="f707e-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="f707e-110">En el cuadro de diálogo **tipo de aplicación** , seleccione **aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="f707e-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="f707e-111">Proporcione un **nombre** para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f707e-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="f707e-112">En la sección **URI de redireccionamiento autorizados** , seleccione **Agregar URI** para establecer el URI de redirección.</span><span class="sxs-lookup"><span data-stu-id="f707e-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="f707e-113">Ejemplo de URI de redireccionamiento: `https://localhost:{PORT}/signin-google` , donde el `{PORT}` marcador de posición es el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f707e-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="f707e-114">Seleccione el botón **crear** .</span><span class="sxs-lookup"><span data-stu-id="f707e-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="f707e-115">Guarde el **identificador de cliente** y el **secreto de cliente** para su uso en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f707e-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="f707e-116">Al implementar el sitio, puede:</span><span class="sxs-lookup"><span data-stu-id="f707e-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="f707e-117">Actualice el URI de redirección de la aplicación en la **consola de Google** para el URI de redireccionamiento implementado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f707e-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="f707e-118">Cree un nuevo registro de la API de Google en la **consola de Google** para la aplicación de producción con su URI de redirección de producción.</span><span class="sxs-lookup"><span data-stu-id="f707e-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="f707e-119">Almacenar el identificador y el secreto de cliente de Google</span><span class="sxs-lookup"><span data-stu-id="f707e-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="f707e-120">Almacenar valores confidenciales como el ID. de cliente de Google y los valores de secreto con el [Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f707e-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f707e-121">En este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="f707e-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="f707e-122">Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="f707e-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="f707e-123">Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Google:ClientId` y `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="f707e-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="f707e-124">Puede administrar las credenciales de API y el uso en la [consola de API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="f707e-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="f707e-125">Configuración de la autenticación de Google</span><span class="sxs-lookup"><span data-stu-id="f707e-125">Configure Google authentication</span></span>

<span data-ttu-id="f707e-126">Agregue el servicio de Google a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f707e-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="f707e-127">Inicio de sesión con Google</span><span class="sxs-lookup"><span data-stu-id="f707e-127">Sign in with Google</span></span>

* <span data-ttu-id="f707e-128">Ejecute la aplicación y haga clic en **iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="f707e-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="f707e-129">Aparece una opción para iniciar sesión con Google.</span><span class="sxs-lookup"><span data-stu-id="f707e-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="f707e-130">Haga clic en el botón **Google** , que redirige a Google para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="f707e-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="f707e-131">Después de escribir las credenciales de Google, se le redirigirá al sitio Web.</span><span class="sxs-lookup"><span data-stu-id="f707e-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="f707e-132">Consulte la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> referencia de la API para obtener más información sobre las opciones de configuración admitidas por la autenticación de Google.</span><span class="sxs-lookup"><span data-stu-id="f707e-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="f707e-133">Se puede usar para solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="f707e-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="f707e-134">Cambiar el URI de devolución de llamada predeterminado</span><span class="sxs-lookup"><span data-stu-id="f707e-134">Change the default callback URI</span></span>

<span data-ttu-id="f707e-135">El segmento URI `/signin-google` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Google.</span><span class="sxs-lookup"><span data-stu-id="f707e-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="f707e-136">Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Google a través de la propiedad heredada <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> ) de la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> clase.</span><span class="sxs-lookup"><span data-stu-id="f707e-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f707e-137">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="f707e-137">Troubleshooting</span></span>

* <span data-ttu-id="f707e-138">Si el inicio de sesión no funciona y no recibe ningún error, cambie al modo de desarrollo para que el problema sea más fácil de depurar.</span><span class="sxs-lookup"><span data-stu-id="f707e-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="f707e-139">Si Identity no se configura mediante una llamada a `services.AddIdentity` en `ConfigureServices` , *se debe proporcionar la opción ' SignInScheme '* al intentar autenticar los resultados.</span><span class="sxs-lookup"><span data-stu-id="f707e-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f707e-140">La plantilla de proyecto que se usa en este tutorial garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="f707e-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="f707e-141">Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* .</span><span class="sxs-lookup"><span data-stu-id="f707e-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f707e-142">Seleccione **aplicar migraciones** para crear la base de datos y actualice la página para continuar después del error.</span><span class="sxs-lookup"><span data-stu-id="f707e-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f707e-143">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f707e-143">Next steps</span></span>

* <span data-ttu-id="f707e-144">En este artículo se ha mostrado cómo puede realizar la autenticación con Google.</span><span class="sxs-lookup"><span data-stu-id="f707e-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="f707e-145">Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f707e-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="f707e-146">Una vez que publique la aplicación en Azure, restablezca `ClientSecret` en la consola de la API de Google.</span><span class="sxs-lookup"><span data-stu-id="f707e-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="f707e-147">Establezca `Authentication:Google:ClientId` y `Authentication:Google:ClientSecret` como configuración de la aplicación en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="f707e-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f707e-148">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="f707e-148">The configuration system is set up to read keys from environment variables.</span></span>
