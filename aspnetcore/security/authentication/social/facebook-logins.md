---
title: Configuración de inicio de sesión externo de Facebook en ASP.NET Core
author: rick-anderson
description: Tutorial con ejemplos de código que muestran la integración de la autenticación de usuarios de cuentas de Facebook en una aplicación de ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: ef664645768dac11d0ed68db03cfbaebbcb8c0d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021723"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="34d61-103">Configuración de inicio de sesión externo de Facebook en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34d61-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="34d61-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34d61-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="34d61-105">En este tutorial con ejemplos de código se muestra cómo habilitar a los usuarios para que inicien sesión con su cuenta de Facebook mediante un proyecto de ejemplo ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="34d61-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="34d61-106">Comenzaremos por crear un identificador de aplicación de Facebook siguiendo los [pasos oficiales](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="34d61-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="34d61-107">Creación de la aplicación en Facebook</span><span class="sxs-lookup"><span data-stu-id="34d61-107">Create the app in Facebook</span></span>

* <span data-ttu-id="34d61-108">Agregue el paquete NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="34d61-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="34d61-109">Vaya a la página de la [aplicación de desarrolladores de Facebook](https://developers.facebook.com/apps/) e inicie sesión.</span><span class="sxs-lookup"><span data-stu-id="34d61-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="34d61-110">Si aún no tiene una cuenta de Facebook, use el vínculo **suscribirse a Facebook** en la página de inicio de sesión para crear una.</span><span class="sxs-lookup"><span data-stu-id="34d61-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="34d61-111">Una vez que tenga una cuenta de Facebook, siga las instrucciones para registrarse como desarrollador de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="34d61-112">En el menú **mis aplicaciones** , seleccione **crear aplicación** para crear un nuevo identificador de aplicación.</span><span class="sxs-lookup"><span data-stu-id="34d61-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![El portal de Facebook para desarrolladores está abierto en Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="34d61-114">Rellene el formulario y pulse el botón **crear ID** . de aplicación.</span><span class="sxs-lookup"><span data-stu-id="34d61-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Crear un nuevo formulario de ID. de aplicación](index/_static/FBNewAppId.png)

* <span data-ttu-id="34d61-116">En la tarjeta nueva aplicación, seleccione **Agregar un producto**.</span><span class="sxs-lookup"><span data-stu-id="34d61-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="34d61-117">En la tarjeta de **Inicio de sesión de Facebook** , haga clic en **configurar** .</span><span class="sxs-lookup"><span data-stu-id="34d61-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Página de configuración del producto](index/_static/FBProductSetup.png)

* <span data-ttu-id="34d61-119">El Asistente de **Inicio rápido** se inicia con **elegir una plataforma** como primera página.</span><span class="sxs-lookup"><span data-stu-id="34d61-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="34d61-120">Omita el asistente por ahora; para ello, haga clic en el vínculo **configuración** de **Inicio de sesión de Facebook** en el menú de la parte inferior izquierda:</span><span class="sxs-lookup"><span data-stu-id="34d61-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Omitir Inicio rápido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="34d61-122">Aparecerá la página Configuración de **OAuth de cliente** :</span><span class="sxs-lookup"><span data-stu-id="34d61-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página de configuración de OAuth de cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="34d61-124">Escriba el URI de desarrollo con */signin-Facebook* anexado en el campo **URI de redirección de OAuth válido** (por ejemplo: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="34d61-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="34d61-125">La autenticación de Facebook configurada más adelante en este tutorial administrará automáticamente las solicitudes en la ruta */signin-Facebook* para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="34d61-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="34d61-126">El URI */signin-Facebook* se establece como la devolución de llamada predeterminada del proveedor de autenticación de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="34d61-127">Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Facebook a través de la propiedad heredada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la clase [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="34d61-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="34d61-128">Haga clic en **Guardar cambios**.</span><span class="sxs-lookup"><span data-stu-id="34d61-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="34d61-129">Haga clic en **configuración**  >  vínculo**básico** en el panel de navegación izquierdo.</span><span class="sxs-lookup"><span data-stu-id="34d61-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="34d61-130">En esta página, tome nota de su `App ID` y su `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="34d61-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="34d61-131">En la siguiente sección, agregará ambos a la aplicación ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="34d61-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="34d61-132">Al implementar el sitio, debe volver a visitar la página de configuración de **Inicio de sesión de Facebook** y registrar un nuevo URI público.</span><span class="sxs-lookup"><span data-stu-id="34d61-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="34d61-133">Almacenar el identificador y el secreto de la aplicación de Facebook</span><span class="sxs-lookup"><span data-stu-id="34d61-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="34d61-134">Almacene la configuración confidencial, como el identificador de la aplicación de Facebook y los valores de secreto con el [Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="34d61-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="34d61-135">En este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="34d61-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="34d61-136">Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="34d61-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="34d61-137">Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Facebook:AppId` y `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="34d61-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="34d61-138">Configuración de la autenticación de Facebook</span><span class="sxs-lookup"><span data-stu-id="34d61-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="34d61-139">Agregue el servicio Facebook en el `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="34d61-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="34d61-140">Inicio de sesión con Facebook</span><span class="sxs-lookup"><span data-stu-id="34d61-140">Sign in with Facebook</span></span>

* <span data-ttu-id="34d61-141">Ejecute la aplicación y seleccione **iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="34d61-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="34d61-142">En **usar otro servicio para iniciar sesión**, seleccione Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="34d61-143">Se le redirigirá a **Facebook** para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="34d61-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="34d61-144">Escriba sus credenciales de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="34d61-145">Se le redirigirá de nuevo al sitio donde puede establecer el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="34d61-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="34d61-146">Ya ha iniciado sesión con sus credenciales de Facebook:</span><span class="sxs-lookup"><span data-stu-id="34d61-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="34d61-147">ReAct para cancelar autorizar el inicio de sesión externo</span><span class="sxs-lookup"><span data-stu-id="34d61-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="34d61-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>puede proporcionar una ruta de acceso de redireccionamiento al agente de usuario cuando el usuario no aprueba la demanda de autorización solicitada.</span><span class="sxs-lookup"><span data-stu-id="34d61-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="34d61-149">El código siguiente establece `AccessDeniedPath` en `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="34d61-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="34d61-150">Se recomienda que la `AccessDeniedPath` página contenga la siguiente información:</span><span class="sxs-lookup"><span data-stu-id="34d61-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="34d61-151">Se canceló la autenticación remota.</span><span class="sxs-lookup"><span data-stu-id="34d61-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="34d61-152">Esta aplicación requiere autenticación.</span><span class="sxs-lookup"><span data-stu-id="34d61-152">This app requires authentication.</span></span>
* <span data-ttu-id="34d61-153">Para volver a intentar iniciar sesión, seleccione el vínculo de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="34d61-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="34d61-154">Probar AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="34d61-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="34d61-155">Vaya a [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="34d61-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="34d61-156">Si ha iniciado sesión, debe cerrar la sesión.</span><span class="sxs-lookup"><span data-stu-id="34d61-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="34d61-157">Ejecute la aplicación y seleccione el inicio de sesión de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="34d61-158">Seleccione **no ahora**.</span><span class="sxs-lookup"><span data-stu-id="34d61-158">Select **Not now**.</span></span> <span data-ttu-id="34d61-159">Se le redirigirá a la `AccessDeniedPath` página especificada.</span><span class="sxs-lookup"><span data-stu-id="34d61-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="34d61-160">Consulte la referencia de la API de [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="34d61-161">Las opciones de configuración se pueden usar para:</span><span class="sxs-lookup"><span data-stu-id="34d61-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="34d61-162">Solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="34d61-162">Request different information about the user.</span></span>
* <span data-ttu-id="34d61-163">Agregue argumentos de cadena de consulta para personalizar la experiencia de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="34d61-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="34d61-164">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="34d61-164">Troubleshooting</span></span>

* <span data-ttu-id="34d61-165">**Solo ASP.net Core 2. x:** Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación.</span><span class="sxs-lookup"><span data-stu-id="34d61-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="34d61-166">La plantilla de proyecto que se usa en este tutorial garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="34d61-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="34d61-167">Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* .</span><span class="sxs-lookup"><span data-stu-id="34d61-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="34d61-168">Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.</span><span class="sxs-lookup"><span data-stu-id="34d61-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="34d61-169">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="34d61-169">Next steps</span></span>

* <span data-ttu-id="34d61-170">En este artículo se ha mostrado cómo puede realizar la autenticación con Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="34d61-171">Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="34d61-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="34d61-172">Una vez que publique el sitio web en la aplicación Web de Azure, debe restablecer el `AppSecret` en el portal para desarrolladores de Facebook.</span><span class="sxs-lookup"><span data-stu-id="34d61-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="34d61-173">Establezca `Authentication:Facebook:AppId` y `Authentication:Facebook:AppSecret` como configuración de la aplicación en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="34d61-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="34d61-174">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="34d61-174">The configuration system is set up to read keys from environment variables.</span></span>
