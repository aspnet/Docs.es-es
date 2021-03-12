---
title: Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación ASP.NET Core con confirmación por correo electrónico y restablecimiento de contraseña.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: f71ae5e619b875c03401fa78320582c406875401
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586116"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="042f9-103">Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="042f9-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="042f9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="042f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="042f9-105">En este tutorial se muestra cómo crear una aplicación ASP.NET Core con confirmación de correo electrónico y restablecimiento de contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="042f9-106">Este tutorial **no** es un tema de inicio.</span><span class="sxs-lookup"><span data-stu-id="042f9-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="042f9-107">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="042f9-107">You should be familiar with:</span></span>

* [<span data-ttu-id="042f9-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="042f9-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="042f9-109">Autenticación</span><span class="sxs-lookup"><span data-stu-id="042f9-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="042f9-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="042f9-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="042f9-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="042f9-111">Prerequisites</span></span>

[<span data-ttu-id="042f9-112">SDK de .NET Core 3.0 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="042f9-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="042f9-113">Crear y probar una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="042f9-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="042f9-114">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="042f9-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="042f9-115">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="042f9-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="042f9-116">Una vez registrado, se le redirigirá a la `/Identity/Account/RegisterConfirmation` página a que contiene un vínculo para simular la confirmación de correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="042f9-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="042f9-117">Seleccione el `Click here to confirm your account` vínculo.</span><span class="sxs-lookup"><span data-stu-id="042f9-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="042f9-118">Seleccione el vínculo de **Inicio de sesión** e inicie sesión con las mismas credenciales.</span><span class="sxs-lookup"><span data-stu-id="042f9-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="042f9-119">Seleccione el `Hello YourEmail@provider.com!` vínculo, que le redirigirá a la `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="042f9-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="042f9-120">Seleccione la pestaña **datos personales** de la izquierda y, a continuación, seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="042f9-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="042f9-121">Configuración de un proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-121">Configure an email provider</span></span>

<span data-ttu-id="042f9-122">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="042f9-123">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="042f9-124">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-124">You can use other email providers.</span></span> <span data-ttu-id="042f9-125">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="042f9-126">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="042f9-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="042f9-127">Es posible que la cuenta de SendGrid necesite [Agregar un remitente](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="042f9-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="042f9-128">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="042f9-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="042f9-129">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="042f9-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="042f9-130">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="042f9-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="042f9-131">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="042f9-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="042f9-132">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="042f9-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="042f9-133">En Windows, el administrador de secretos almacena pares de clave/valor en un *secrets.jsen* el archivo en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="042f9-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="042f9-134">No se cifra el contenido de la *secrets.jsen* el archivo.</span><span class="sxs-lookup"><span data-stu-id="042f9-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="042f9-135">En el marcado siguiente se muestra el *secrets.jsen* el archivo.</span><span class="sxs-lookup"><span data-stu-id="042f9-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="042f9-136">Se ha `SendGridKey` quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="042f9-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="042f9-137">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="042f9-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="042f9-138">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="042f9-138">Install SendGrid</span></span>

<span data-ttu-id="042f9-139">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="042f9-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="042f9-140">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="042f9-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="042f9-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="042f9-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="042f9-142">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="042f9-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="042f9-143">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="042f9-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="042f9-144">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="042f9-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="042f9-145">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="042f9-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="042f9-146">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="042f9-146">Implement IEmailSender</span></span>

<span data-ttu-id="042f9-147">Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="042f9-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="042f9-148">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-148">Configure startup to support email</span></span>

<span data-ttu-id="042f9-149">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="042f9-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="042f9-150">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="042f9-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="042f9-151">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="042f9-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="042f9-152">Scaffold RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="042f9-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="042f9-153">Siga las instrucciones de [scaffolding Identity ](xref:security/authentication/scaffold-identity) y scaffold `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="042f9-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="042f9-154">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="042f9-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="042f9-155">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="042f9-156">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="042f9-156">Run the app and register a new user</span></span>
* <span data-ttu-id="042f9-157">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="042f9-158">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="042f9-159">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="042f9-160">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="042f9-161">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="042f9-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="042f9-162">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="042f9-162">Test password reset</span></span>

* <span data-ttu-id="042f9-163">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="042f9-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="042f9-164">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="042f9-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="042f9-165">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="042f9-166">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="042f9-167">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="042f9-168">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="042f9-169">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-169">Resend email confirmation</span></span>

<span data-ttu-id="042f9-170">En ASP.NET Core 5,0 y versiones posteriores, seleccione el vínculo **reenviar confirmación de correo electrónico** en la página de **Inicio de sesión** .</span><span class="sxs-lookup"><span data-stu-id="042f9-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="042f9-171">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="042f9-171">Change email and activity timeout</span></span>

<span data-ttu-id="042f9-172">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="042f9-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="042f9-173">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="042f9-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="042f9-174">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="042f9-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="042f9-175">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="042f9-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="042f9-176">Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.</span><span class="sxs-lookup"><span data-stu-id="042f9-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="042f9-177">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-177">Change the email token lifespan</span></span>

<span data-ttu-id="042f9-178">La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="042f9-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="042f9-179">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="042f9-180">Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="042f9-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="042f9-181">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="042f9-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="042f9-182">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-182">Debug email</span></span>

<span data-ttu-id="042f9-183">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="042f9-183">If you can't get email working:</span></span>

* <span data-ttu-id="042f9-184">Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.</span><span class="sxs-lookup"><span data-stu-id="042f9-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="042f9-185">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="042f9-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="042f9-186">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="042f9-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="042f9-187">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="042f9-187">Check your spam folder.</span></span>
* <span data-ttu-id="042f9-188">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="042f9-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="042f9-189">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="042f9-190">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="042f9-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="042f9-191">Si publica la aplicación en Azure, establezca los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="042f9-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="042f9-192">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="042f9-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="042f9-193">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="042f9-193">Combine social and local login accounts</span></span>

<span data-ttu-id="042f9-194">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="042f9-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="042f9-195">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="042f9-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="042f9-196">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="042f9-197">En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="042f9-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="042f9-199">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="042f9-199">Click on the **Manage** link.</span></span> <span data-ttu-id="042f9-200">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-200">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="042f9-202">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="042f9-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="042f9-203">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="042f9-203">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="042f9-205">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="042f9-205">The two accounts have been combined.</span></span> <span data-ttu-id="042f9-206">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="042f9-206">You are able to sign in with either account.</span></span> <span data-ttu-id="042f9-207">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="042f9-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="042f9-208">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="042f9-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="042f9-209">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="042f9-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="042f9-210">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="042f9-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="042f9-211">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="042f9-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="042f9-212">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="042f9-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="042f9-213">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="042f9-213">Confirm existing users.</span></span> <span data-ttu-id="042f9-214">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="042f9-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="042f9-215">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="042f9-215">Prerequisites</span></span>

[<span data-ttu-id="042f9-216">SDK de .NET Core 2,2 o posterior</span><span class="sxs-lookup"><span data-stu-id="042f9-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="042f9-217">Creación de una aplicación web y scaffolding Identity</span><span class="sxs-lookup"><span data-stu-id="042f9-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="042f9-218">Ejecute los siguientes comandos para crear una aplicación web con autenticación.</span><span class="sxs-lookup"><span data-stu-id="042f9-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="042f9-219">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="042f9-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="042f9-220">Una `InputModel` `Password` propiedad se encuentra en el `Areas/Identity/Pages/Account/Register.cshtml.cs` archivo después de la técnica scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="042f9-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="042f9-221">Prueba del nuevo registro de usuario</span><span class="sxs-lookup"><span data-stu-id="042f9-221">Test new user registration</span></span>

<span data-ttu-id="042f9-222">Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="042f9-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="042f9-223">En este momento, la única validación en el correo electrónico es con el [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="042f9-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="042f9-224">Después de enviar el registro, ha iniciado sesión en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="042f9-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="042f9-225">Más adelante en el tutorial, se actualiza el código para que los nuevos usuarios no puedan iniciar sesión hasta que se valide su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="042f9-226">Tenga en cuenta que el campo de la tabla `EmailConfirmed` es `False` .</span><span class="sxs-lookup"><span data-stu-id="042f9-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="042f9-227">Es posible que desee usar este correo electrónico de nuevo en el paso siguiente cuando la aplicación envíe un correo electrónico de confirmación.</span><span class="sxs-lookup"><span data-stu-id="042f9-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="042f9-228">Haga clic con el botón derecho en la fila y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="042f9-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="042f9-229">Al eliminar el alias de correo electrónico, se facilitan los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="042f9-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="042f9-230">Requerir confirmación de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-230">Require email confirmation</span></span>

<span data-ttu-id="042f9-231">Se recomienda confirmar el correo electrónico de un nuevo registro de usuario.</span><span class="sxs-lookup"><span data-stu-id="042f9-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="042f9-232">La confirmación por correo electrónico ayuda a comprobar que no está suplantando a otra persona (es decir, que no se han registrado con el correo electrónico de otro usuario).</span><span class="sxs-lookup"><span data-stu-id="042f9-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="042f9-233">Supongamos que tiene un foro de discusión y desea evitar que " yli@example.com " se registre como " nolivetto@contoso.com ".</span><span class="sxs-lookup"><span data-stu-id="042f9-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="042f9-234">Sin confirmación por correo electrónico, " nolivetto@contoso.com " podría recibir correo electrónico no deseado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="042f9-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="042f9-235">Supongamos que el usuario se registró accidentalmente como " ylo@example.com " y no detectó la falta de ortografía de "Yli".</span><span class="sxs-lookup"><span data-stu-id="042f9-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="042f9-236">No podrán usar la recuperación de contraseña porque la aplicación no tiene el correo electrónico correcto.</span><span class="sxs-lookup"><span data-stu-id="042f9-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="042f9-237">La confirmación por correo electrónico proporciona una protección limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="042f9-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="042f9-238">La confirmación por correo electrónico no proporciona protección contra usuarios malintencionados con muchas cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="042f9-239">Por lo general, querrá evitar que los nuevos usuarios publiquen datos en el sitio Web antes de que tengan un correo electrónico confirmado.</span><span class="sxs-lookup"><span data-stu-id="042f9-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="042f9-240">Actualización `Startup.ConfigureServices`  para requerir un correo electrónico confirmado:</span><span class="sxs-lookup"><span data-stu-id="042f9-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="042f9-241">`config.SignIn.RequireConfirmedEmail = true;` impide que los usuarios registrados inicien sesión hasta que se confirme su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="042f9-242">Configurar proveedor de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-242">Configure email provider</span></span>

<span data-ttu-id="042f9-243">En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="042f9-244">Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="042f9-245">Puede usar otros proveedores de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-245">You can use other email providers.</span></span> <span data-ttu-id="042f9-246">ASP.NET Core 2. x incluye `System.Net.Mail` , que le permite enviar correo electrónico desde su aplicación.</span><span class="sxs-lookup"><span data-stu-id="042f9-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="042f9-247">Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="042f9-248">SMTP es difícil de proteger y configurar correctamente.</span><span class="sxs-lookup"><span data-stu-id="042f9-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="042f9-249">Cree una clase para capturar la clave de correo electrónico segura.</span><span class="sxs-lookup"><span data-stu-id="042f9-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="042f9-250">Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:</span><span class="sxs-lookup"><span data-stu-id="042f9-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="042f9-251">Configuración de secretos de usuario de SendGrid</span><span class="sxs-lookup"><span data-stu-id="042f9-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="042f9-252">Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="042f9-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="042f9-253">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="042f9-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="042f9-254">En Windows, el administrador de secretos almacena pares de clave/valor en un *secrets.jsen* el archivo en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.</span><span class="sxs-lookup"><span data-stu-id="042f9-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="042f9-255">No se cifra el contenido de la *secrets.jsen* el archivo.</span><span class="sxs-lookup"><span data-stu-id="042f9-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="042f9-256">En el marcado siguiente se muestra el *secrets.jsen* el archivo.</span><span class="sxs-lookup"><span data-stu-id="042f9-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="042f9-257">Se ha `SendGridKey` quitado el valor.</span><span class="sxs-lookup"><span data-stu-id="042f9-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="042f9-258">Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="042f9-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="042f9-259">Instalación de SendGrid</span><span class="sxs-lookup"><span data-stu-id="042f9-259">Install SendGrid</span></span>

<span data-ttu-id="042f9-260">En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="042f9-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="042f9-261">Instale el `SendGrid` paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="042f9-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="042f9-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="042f9-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="042f9-263">En la consola del administrador de paquetes, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="042f9-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="042f9-264">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="042f9-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="042f9-265">En la consola de, escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="042f9-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="042f9-266">Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.</span><span class="sxs-lookup"><span data-stu-id="042f9-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="042f9-267">Implementación de IEmailSender</span><span class="sxs-lookup"><span data-stu-id="042f9-267">Implement IEmailSender</span></span>

<span data-ttu-id="042f9-268">Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="042f9-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="042f9-269">Configurar el inicio para admitir correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-269">Configure startup to support email</span></span>

<span data-ttu-id="042f9-270">Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="042f9-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="042f9-271">Agregue `EmailSender` como un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="042f9-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="042f9-272">Registre la `AuthMessageSenderOptions` instancia de configuración.</span><span class="sxs-lookup"><span data-stu-id="042f9-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="042f9-273">Habilitar la confirmación de la cuenta y la recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="042f9-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="042f9-274">La plantilla tiene el código para la confirmación de la cuenta y la recuperación de la contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="042f9-275">Busque el `OnPostAsync` método en *areas/ Identity /pages/Account/Register.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="042f9-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="042f9-276">Evite que los usuarios recién registrados inicien sesión automáticamente al comentar la siguiente línea:</span><span class="sxs-lookup"><span data-stu-id="042f9-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="042f9-277">El método complete se muestra con la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="042f9-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="042f9-278">Registro, confirmación de correo electrónico y restablecimiento de contraseña</span><span class="sxs-lookup"><span data-stu-id="042f9-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="042f9-279">Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="042f9-280">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="042f9-280">Run the app and register a new user</span></span>
* <span data-ttu-id="042f9-281">Compruebe su correo electrónico para el vínculo de confirmación de la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="042f9-282">Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="042f9-283">Haga clic en el vínculo para confirmar el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="042f9-284">Inicie sesión con su correo electrónico y contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="042f9-285">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="042f9-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="042f9-286">Ver la página administrar</span><span class="sxs-lookup"><span data-stu-id="042f9-286">View the manage page</span></span>

<span data-ttu-id="042f9-287">Seleccione el nombre de usuario en el explorador: ![ ventana del explorador con el nombre de usuario](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="042f9-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="042f9-288">La página Administrar se muestra con la pestaña **perfil** seleccionada.</span><span class="sxs-lookup"><span data-stu-id="042f9-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="042f9-289">El **correo electrónico** muestra una casilla que indica que se ha confirmado el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="042f9-290">Restablecimiento de la contraseña de prueba</span><span class="sxs-lookup"><span data-stu-id="042f9-290">Test password reset</span></span>

* <span data-ttu-id="042f9-291">Si ha iniciado sesión, seleccione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="042f9-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="042f9-292">Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .</span><span class="sxs-lookup"><span data-stu-id="042f9-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="042f9-293">Escriba el correo electrónico que usó para registrar la cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="042f9-294">Se envía un correo electrónico con un vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="042f9-295">Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="042f9-296">Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.</span><span class="sxs-lookup"><span data-stu-id="042f9-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="042f9-297">Cambiar el tiempo de espera del correo electrónico y la actividad</span><span class="sxs-lookup"><span data-stu-id="042f9-297">Change email and activity timeout</span></span>

<span data-ttu-id="042f9-298">El tiempo de espera de inactividad predeterminado es de 14 días.</span><span class="sxs-lookup"><span data-stu-id="042f9-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="042f9-299">El código siguiente establece el tiempo de espera de inactividad en 5 días:</span><span class="sxs-lookup"><span data-stu-id="042f9-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="042f9-300">Cambiar todas las vigencias de los tokens de protección de datos</span><span class="sxs-lookup"><span data-stu-id="042f9-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="042f9-301">El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:</span><span class="sxs-lookup"><span data-stu-id="042f9-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="042f9-302">Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.</span><span class="sxs-lookup"><span data-stu-id="042f9-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="042f9-303">Cambiar la duración del token de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-303">Change the email token lifespan</span></span>

<span data-ttu-id="042f9-304">La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="042f9-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="042f9-305">En esta sección se muestra cómo cambiar la duración del token de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="042f9-306">Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="042f9-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="042f9-307">Agregue el proveedor personalizado al contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="042f9-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="042f9-308">Confirmación de reenvío de correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-308">Resend email confirmation</span></span>

<span data-ttu-id="042f9-309">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="042f9-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="042f9-310">Depurar correo electrónico</span><span class="sxs-lookup"><span data-stu-id="042f9-310">Debug email</span></span>

<span data-ttu-id="042f9-311">Si no consigue que el correo electrónico funcione:</span><span class="sxs-lookup"><span data-stu-id="042f9-311">If you can't get email working:</span></span>

* <span data-ttu-id="042f9-312">Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.</span><span class="sxs-lookup"><span data-stu-id="042f9-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="042f9-313">Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="042f9-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="042f9-314">Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="042f9-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="042f9-315">Compruebe la carpeta de correo no deseado.</span><span class="sxs-lookup"><span data-stu-id="042f9-315">Check your spam folder.</span></span>
* <span data-ttu-id="042f9-316">Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).</span><span class="sxs-lookup"><span data-stu-id="042f9-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="042f9-317">Intente enviar a diferentes cuentas de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="042f9-318">**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="042f9-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="042f9-319">Si publica la aplicación en Azure, puede establecer los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure.</span><span class="sxs-lookup"><span data-stu-id="042f9-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="042f9-320">El sistema de configuración está configurado para leer las claves de las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="042f9-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="042f9-321">Combinación de cuentas de inicio de sesión sociales y locales</span><span class="sxs-lookup"><span data-stu-id="042f9-321">Combine social and local login accounts</span></span>

<span data-ttu-id="042f9-322">Para completar esta sección, primero debe habilitar un proveedor de autenticación externo.</span><span class="sxs-lookup"><span data-stu-id="042f9-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="042f9-323">Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="042f9-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="042f9-324">Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="042f9-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="042f9-325">En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.</span><span class="sxs-lookup"><span data-stu-id="042f9-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="042f9-327">Haga clic en el vínculo **administrar** .</span><span class="sxs-lookup"><span data-stu-id="042f9-327">Click on the **Manage** link.</span></span> <span data-ttu-id="042f9-328">Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.</span><span class="sxs-lookup"><span data-stu-id="042f9-328">Note the 0 external (social logins) associated with this account.</span></span>

![Administrar vista](accconfirm/_static/manage.png)

<span data-ttu-id="042f9-330">Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="042f9-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="042f9-331">En la imagen siguiente, Facebook es el proveedor de autenticación externo:</span><span class="sxs-lookup"><span data-stu-id="042f9-331">In the following image, Facebook is the external authentication provider:</span></span>

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="042f9-333">Las dos cuentas se han combinado.</span><span class="sxs-lookup"><span data-stu-id="042f9-333">The two accounts have been combined.</span></span> <span data-ttu-id="042f9-334">Puede iniciar sesión con cualquiera de las dos cuentas.</span><span class="sxs-lookup"><span data-stu-id="042f9-334">You are able to sign in with either account.</span></span> <span data-ttu-id="042f9-335">Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="042f9-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="042f9-336">Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios</span><span class="sxs-lookup"><span data-stu-id="042f9-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="042f9-337">La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="042f9-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="042f9-338">Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas.</span><span class="sxs-lookup"><span data-stu-id="042f9-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="042f9-339">Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="042f9-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="042f9-340">Actualice la base de datos para marcar todos los usuarios existentes como confirmados.</span><span class="sxs-lookup"><span data-stu-id="042f9-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="042f9-341">Confirme los usuarios existentes.</span><span class="sxs-lookup"><span data-stu-id="042f9-341">Confirm existing users.</span></span> <span data-ttu-id="042f9-342">Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.</span><span class="sxs-lookup"><span data-stu-id="042f9-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
