---
title: Configuración de inicio de sesión externo de Twitter con ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra la integración de la autenticación de usuarios de cuentas de Twitter en una aplicación ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- 'appsettings.json'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053285"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="dd674-103">Configuración de inicio de sesión externo de Twitter con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd674-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="dd674-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dd674-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dd674-105">Este ejemplo muestra cómo permitir a los usuarios [iniciar sesión con su cuenta de Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) mediante un proyecto de ejemplo ASP.net Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dd674-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="dd674-106">Creación de la aplicación en Twitter</span><span class="sxs-lookup"><span data-stu-id="dd674-106">Create the app in Twitter</span></span>

* <span data-ttu-id="dd674-107">Agregue el paquete de NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="dd674-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="dd674-108">Vaya a [https://apps.twitter.com/](https://apps.twitter.com/) e inicie sesión.</span><span class="sxs-lookup"><span data-stu-id="dd674-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="dd674-109">Si aún no tiene una cuenta de Twitter, use el vínculo **[Regístrese ahora](https://twitter.com/signup)** para crear una.</span><span class="sxs-lookup"><span data-stu-id="dd674-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="dd674-110">Seleccione **crear una aplicación** .</span><span class="sxs-lookup"><span data-stu-id="dd674-110">Select **Create an app** .</span></span> <span data-ttu-id="dd674-111">Rellene el **nombre** de la aplicación, la descripción de la **aplicación** y el URI del **sitio web** público (esto puede ser temporal hasta que registre el nombre de dominio):</span><span class="sxs-lookup"><span data-stu-id="dd674-111">Fill out the **App name** , **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="dd674-112">Active la casilla situada junto a **Habilitar el inicio de sesión con Twitter** .</span><span class="sxs-lookup"><span data-stu-id="dd674-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="dd674-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="dd674-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="dd674-114">requiere que los usuarios tengan una dirección de correo electrónico de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="dd674-114">requires users to have an email address by default.</span></span> <span data-ttu-id="dd674-115">Vaya a la pestaña **permisos** , haga clic en el botón **Editar** y active la casilla situada junto a **solicitar dirección de correo electrónico de los usuarios** .</span><span class="sxs-lookup"><span data-stu-id="dd674-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users** .</span></span>

* <span data-ttu-id="dd674-116">Escriba el URI de desarrollo con `/signin-twitter` anexado en el campo **URL de devolución de llamada** (por ejemplo: `https://webapp128.azurewebsites.net/signin-twitter` ).</span><span class="sxs-lookup"><span data-stu-id="dd674-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="dd674-117">El esquema de autenticación de Twitter configurado más adelante en este ejemplo administrará automáticamente las solicitudes de `/signin-twitter` la ruta para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="dd674-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="dd674-118">El segmento URI `/signin-twitter` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Twitter.</span><span class="sxs-lookup"><span data-stu-id="dd674-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="dd674-119">Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Twitter a través de la propiedad heredada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la clase [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="dd674-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="dd674-120">Rellene el resto del formulario y seleccione **crear** .</span><span class="sxs-lookup"><span data-stu-id="dd674-120">Fill out the rest of the form and select **Create** .</span></span> <span data-ttu-id="dd674-121">Se muestran los nuevos detalles de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="dd674-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="dd674-122">Almacenar la clave y el secreto de la API de consumidor de Twitter</span><span class="sxs-lookup"><span data-stu-id="dd674-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="dd674-123">Almacene la configuración confidencial, como la clave de API de consumidor de Twitter y el secreto con el [Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dd674-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="dd674-124">En este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="dd674-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="dd674-125">Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="dd674-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="dd674-126">Almacene la configuración confidencial en el almacén secreto local con las claves de secretos `Authentication:Twitter:ConsumerKey` y `Authentication:Twitter:ConsumerSecret` :</span><span class="sxs-lookup"><span data-stu-id="dd674-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="dd674-127">Estos tokens se pueden encontrar en la pestaña **claves y tokens de acceso** después de crear una nueva aplicación de Twitter:</span><span class="sxs-lookup"><span data-stu-id="dd674-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="dd674-128">Configuración de la autenticación de Twitter</span><span class="sxs-lookup"><span data-stu-id="dd674-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="dd674-129">Agregue el servicio Twitter en el `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="dd674-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="dd674-130">Consulte la referencia de la API de [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para más información sobre las opciones de configuración admitidas por la autenticación de Twitter.</span><span class="sxs-lookup"><span data-stu-id="dd674-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="dd674-131">Se puede usar para solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="dd674-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="dd674-132">Inicio de sesión con Twitter</span><span class="sxs-lookup"><span data-stu-id="dd674-132">Sign in with Twitter</span></span>

<span data-ttu-id="dd674-133">Ejecute la aplicación y seleccione **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="dd674-133">Run the app and select **Log in** .</span></span> <span data-ttu-id="dd674-134">Aparece una opción para iniciar sesión con Twitter:</span><span class="sxs-lookup"><span data-stu-id="dd674-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="dd674-135">Al hacer clic en **Twitter** , se redirige a Twitter para la autenticación:</span><span class="sxs-lookup"><span data-stu-id="dd674-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="dd674-136">Después de escribir sus credenciales de Twitter, se le redirigirá al sitio web donde puede establecer el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="dd674-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="dd674-137">Ya ha iniciado sesión con sus credenciales de Twitter:</span><span class="sxs-lookup"><span data-stu-id="dd674-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="dd674-138">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="dd674-138">Troubleshooting</span></span>

* <span data-ttu-id="dd674-139">**Solo ASP.net Core 2. x:** Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación.</span><span class="sxs-lookup"><span data-stu-id="dd674-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="dd674-140">La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="dd674-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="dd674-141">Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* .</span><span class="sxs-lookup"><span data-stu-id="dd674-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="dd674-142">Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.</span><span class="sxs-lookup"><span data-stu-id="dd674-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dd674-143">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="dd674-143">Next steps</span></span>

* <span data-ttu-id="dd674-144">En este artículo se ha mostrado cómo puede realizar la autenticación con Twitter.</span><span class="sxs-lookup"><span data-stu-id="dd674-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="dd674-145">Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dd674-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="dd674-146">Una vez que publique el sitio web en la aplicación Web de Azure, debe restablecer el `ConsumerSecret` en el portal para desarrolladores de Twitter.</span><span class="sxs-lookup"><span data-stu-id="dd674-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="dd674-147">Establezca `Authentication:Twitter:ConsumerKey` y `Authentication:Twitter:ConsumerSecret` como configuración de la aplicación en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="dd674-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="dd674-148">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="dd674-148">The configuration system is set up to read keys from environment variables.</span></span>
