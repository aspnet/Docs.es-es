---
title: Configuración de inicio de sesión externo de cuenta de Microsoft con ASP.NET Core
author: rick-anderson
description: En este ejemplo se muestra la integración de cuenta de Microsoft la autenticación de usuario en una aplicación de ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3430d842b6a4f7da30370977f72e6f132e28bb7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634259"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="beca1-103">Configuración de inicio de sesión externo de cuenta de Microsoft con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="beca1-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="beca1-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="beca1-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="beca1-105">Este ejemplo muestra cómo permitir a los usuarios iniciar sesión con su cuenta de Microsoft profesional, educativa o personal mediante el proyecto ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="beca1-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="beca1-106">Creación de la aplicación en el portal para desarrolladores de Microsoft</span><span class="sxs-lookup"><span data-stu-id="beca1-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="beca1-107">Agregue el paquete NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="beca1-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="beca1-108">Vaya a la página [de registros de aplicaciones de Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) y cree o inicie sesión en un cuenta de Microsoft:</span><span class="sxs-lookup"><span data-stu-id="beca1-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="beca1-109">Si no tiene un cuenta de Microsoft, seleccione **crear uno**.</span><span class="sxs-lookup"><span data-stu-id="beca1-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="beca1-110">Después de iniciar sesión, se le redirigirá a la página **registros de aplicaciones** :</span><span class="sxs-lookup"><span data-stu-id="beca1-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="beca1-111">Seleccionar **nuevo registro**</span><span class="sxs-lookup"><span data-stu-id="beca1-111">Select **New registration**</span></span>
* <span data-ttu-id="beca1-112">Escriba un **nombre**.</span><span class="sxs-lookup"><span data-stu-id="beca1-112">Enter a **Name**.</span></span>
* <span data-ttu-id="beca1-113">Seleccione una opción para los **tipos de cuenta compatibles**.</span><span class="sxs-lookup"><span data-stu-id="beca1-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="beca1-114">En **URI de redirección**, escriba la dirección URL de desarrollo con `/signin-microsoft` anexado.</span><span class="sxs-lookup"><span data-stu-id="beca1-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="beca1-115">Por ejemplo, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="beca1-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="beca1-116">El esquema de autenticación de Microsoft configurado más adelante en este ejemplo administrará automáticamente las solicitudes en `/signin-microsoft` la ruta para implementar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="beca1-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="beca1-117">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="beca1-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="beca1-118">Creación de un secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="beca1-118">Create client secret</span></span>

* <span data-ttu-id="beca1-119">En el panel izquierdo, seleccione **Certificados y secretos**.</span><span class="sxs-lookup"><span data-stu-id="beca1-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="beca1-120">En **secretos de cliente**, seleccione **nuevo secreto de cliente** .</span><span class="sxs-lookup"><span data-stu-id="beca1-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="beca1-121">Agregue una descripción para el secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="beca1-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="beca1-122">Seleccione el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="beca1-122">Select the **Add** button.</span></span>

* <span data-ttu-id="beca1-123">En **secretos de cliente**, copie el valor del secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="beca1-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="beca1-124">El segmento URI `/signin-microsoft` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="beca1-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="beca1-125">Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Microsoft a través de la propiedad [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="beca1-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="beca1-126">Almacenar el identificador y el secreto de cliente de Microsoft</span><span class="sxs-lookup"><span data-stu-id="beca1-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="beca1-127">Almacene la configuración confidencial, como los valores de identificador de cliente y secreto de Microsoft, con el [Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="beca1-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="beca1-128">En este ejemplo, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="beca1-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="beca1-129">Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="beca1-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="beca1-130">Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Microsoft:ClientId` y `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="beca1-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="beca1-131">Configurar la autenticación de la cuenta Microsoft</span><span class="sxs-lookup"><span data-stu-id="beca1-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="beca1-132">Agregue el servicio de la cuenta de Microsoft a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="beca1-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="beca1-133">Para obtener más información sobre las opciones de configuración admitidas por la autenticación de la cuenta de Microsoft, consulte la referencia de la API de [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="beca1-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="beca1-134">Se puede usar para solicitar información diferente sobre el usuario.</span><span class="sxs-lookup"><span data-stu-id="beca1-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="beca1-135">Iniciar sesión con Microsoft cuenta</span><span class="sxs-lookup"><span data-stu-id="beca1-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="beca1-136">Ejecute la aplicación y haga clic en **iniciar sesión**.</span><span class="sxs-lookup"><span data-stu-id="beca1-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="beca1-137">Aparece una opción para iniciar sesión con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="beca1-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="beca1-138">Al hacer clic en Microsoft, se le redirigirá a Microsoft para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="beca1-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="beca1-139">Después de iniciar sesión con su cuenta de Microsoft, se le pedirá que permita que la aplicación acceda a su información:</span><span class="sxs-lookup"><span data-stu-id="beca1-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="beca1-140">Puntee en **sí** y se le redirigirá de nuevo al sitio web donde puede establecer el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="beca1-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="beca1-141">Ya ha iniciado sesión con sus credenciales de Microsoft:</span><span class="sxs-lookup"><span data-stu-id="beca1-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="beca1-142">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="beca1-142">Troubleshooting</span></span>

* <span data-ttu-id="beca1-143">Si el proveedor de la cuenta de Microsoft le redirige a una página de error de inicio de sesión, tenga en cuenta los parámetros de cadena de consulta título y descripción del error directamente después de `#` (hashtag) en el URI.</span><span class="sxs-lookup"><span data-stu-id="beca1-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="beca1-144">Aunque el mensaje de error parece indicar un problema con la autenticación de Microsoft, la causa más común es que el URI de la aplicación no coincida con ninguno de los **URI de redirección** especificados para la plataforma **Web** .</span><span class="sxs-lookup"><span data-stu-id="beca1-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="beca1-145">Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación.</span><span class="sxs-lookup"><span data-stu-id="beca1-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="beca1-146">La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.</span><span class="sxs-lookup"><span data-stu-id="beca1-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="beca1-147">Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* .</span><span class="sxs-lookup"><span data-stu-id="beca1-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="beca1-148">Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.</span><span class="sxs-lookup"><span data-stu-id="beca1-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="beca1-149">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="beca1-149">Next steps</span></span>

* <span data-ttu-id="beca1-150">En este artículo se ha mostrado cómo puede autenticarse con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="beca1-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="beca1-151">Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="beca1-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="beca1-152">Una vez que publique el sitio web en la aplicación Web de Azure, cree un nuevo secreto de cliente en el portal para desarrolladores de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="beca1-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="beca1-153">Establezca `Authentication:Microsoft:ClientId` y `Authentication:Microsoft:ClientSecret` como configuración de la aplicación en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="beca1-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="beca1-154">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="beca1-154">The configuration system is set up to read keys from environment variables.</span></span>
