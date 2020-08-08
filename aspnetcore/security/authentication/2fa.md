---
title: Autenticación en dos fases con SMS en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar la autenticación en dos fases (2FA) con una aplicación ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 28aef65234eaf162ba6e18a2594feb575c93b02f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019496"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="27583-103">Autenticación en dos fases con SMS en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27583-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="27583-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Suiza-desarrolladores](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="27583-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="27583-105">Las aplicaciones de autenticación en dos fases (2FA), con un algoritmo de contraseña de un solo uso (TOTP), son el enfoque recomendado del sector para 2FA.</span><span class="sxs-lookup"><span data-stu-id="27583-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="27583-106">se prefiere 2FA con TOTP a SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="27583-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="27583-107">Para obtener más información, consulte [habilitación de la generación de código QR para aplicaciones TOTP Authenticator en ASP.net Core](xref:security/authentication/identity-enable-qrcodes) para ASP.net Core 2,0 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="27583-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="27583-108">En este tutorial se muestra cómo configurar la autenticación en dos fases (2FA) mediante SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="27583-109">Se proporcionan instrucciones para [Twilio](https://www.twilio.com/) y [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), pero puede usar cualquier otro proveedor de SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="27583-110">Se recomienda completar la [confirmación de la cuenta y la recuperación de la contraseña antes de](xref:security/authentication/accconfirm) iniciar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="27583-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="27583-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="27583-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="27583-112">[Cómo descargar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="27583-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="27583-113">Crear un nuevo proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27583-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="27583-114">Cree una nueva aplicación Web de ASP.NET Core denominada `Web2FA` con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="27583-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="27583-115">Siga las instrucciones de <xref:security/enforcing-ssl> para configurar y requerir https.</span><span class="sxs-lookup"><span data-stu-id="27583-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="27583-116">Crear una cuenta de SMS</span><span class="sxs-lookup"><span data-stu-id="27583-116">Create an SMS account</span></span>

<span data-ttu-id="27583-117">Cree una cuenta de SMS, por ejemplo, de [Twilio](https://www.twilio.com/) o [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="27583-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="27583-118">Grabe las credenciales de autenticación (para Twilio: accountSid y authToken, para ASPSMS: Userkey y password).</span><span class="sxs-lookup"><span data-stu-id="27583-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="27583-119">Determinar las credenciales del proveedor de SMS</span><span class="sxs-lookup"><span data-stu-id="27583-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="27583-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="27583-120">**Twilio:**</span></span>

<span data-ttu-id="27583-121">En la pestaña panel de la cuenta de Twilio, copie el SID de la **cuenta** y el **token de autenticación**.</span><span class="sxs-lookup"><span data-stu-id="27583-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="27583-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="27583-122">**ASPSMS:**</span></span>

<span data-ttu-id="27583-123">En la configuración de la cuenta, vaya a **Userkey** y cópiela junto con la **contraseña**.</span><span class="sxs-lookup"><span data-stu-id="27583-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="27583-124">Más adelante almacenaremos estos valores en con la herramienta de administrador de secretos dentro de las claves `SMSAccountIdentification` y `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="27583-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="27583-125">Especificar SenderID/originador</span><span class="sxs-lookup"><span data-stu-id="27583-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="27583-126">**Twilio:** En la pestaña números, copie el **número de teléfono**de Twilio.</span><span class="sxs-lookup"><span data-stu-id="27583-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="27583-127">**ASPSMS:** En el menú desbloquear orígenes, desbloquee uno o más originadores o elija un originador alfanumérico (no admitido por todas las redes).</span><span class="sxs-lookup"><span data-stu-id="27583-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="27583-128">Más adelante se almacenará este valor con la herramienta de administrador de secretos dentro de la clave `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="27583-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="27583-129">Proporcionar credenciales para el servicio de SMS</span><span class="sxs-lookup"><span data-stu-id="27583-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="27583-130">Usaremos el [patrón de opciones](xref:fundamentals/configuration/options) para tener acceso a la configuración de clave y cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="27583-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="27583-131">Cree una clase para capturar la clave segura de SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="27583-132">En este ejemplo, la `SMSoptions` clase se crea en el archivo *Services/SMSoptions. CS* .</span><span class="sxs-lookup"><span data-stu-id="27583-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="27583-133">Establezca `SMSAccountIdentification` `SMSAccountPassword` y `SMSAccountFrom` con la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="27583-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="27583-134">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="27583-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="27583-135">Agregue el paquete NuGet para el proveedor de SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="27583-136">En la consola del administrador de paquetes (PMC), ejecute:</span><span class="sxs-lookup"><span data-stu-id="27583-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="27583-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="27583-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="27583-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="27583-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="27583-139">Agregue código en el archivo *Services/MessageServices. CS* para habilitar SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="27583-140">Use la sección Twilio o ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="27583-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="27583-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="27583-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="27583-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="27583-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="27583-143">Configurar el inicio para usar`SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="27583-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="27583-144">Agregue `SMSoptions` al contenedor de servicios en el `ConfigureServices` método en *Startup.CS*:</span><span class="sxs-lookup"><span data-stu-id="27583-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="27583-145">Habilitación de la autenticación en dos fases</span><span class="sxs-lookup"><span data-stu-id="27583-145">Enable two-factor authentication</span></span>

<span data-ttu-id="27583-146">Abra el archivo de vista *views/Manage/index. cshtml* Razor y quite los caracteres de comentario (por lo que no hay marcado como comentario).</span><span class="sxs-lookup"><span data-stu-id="27583-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="27583-147">Inicio de sesión con la autenticación en dos fases</span><span class="sxs-lookup"><span data-stu-id="27583-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="27583-148">Ejecutar la aplicación y registrar un nuevo usuario</span><span class="sxs-lookup"><span data-stu-id="27583-148">Run the app and register a new user</span></span>

![Vista de registro de aplicación web abierta en Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="27583-150">Puntee en el nombre de usuario, que activa el `Index` método de acción en el controlador de administración.</span><span class="sxs-lookup"><span data-stu-id="27583-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="27583-151">A continuación, puntee en el vínculo número de teléfono para **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="27583-151">Then tap the phone number **Add** link.</span></span>

![Administrar vista: Pulse el vínculo "agregar".](2fa/_static/login2fa2.png)

* <span data-ttu-id="27583-153">Agregue un número de teléfono que recibirá el código de verificación y pulse **Enviar código de verificación**.</span><span class="sxs-lookup"><span data-stu-id="27583-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Página Agregar número de teléfono](2fa/_static/login2fa3.png)

* <span data-ttu-id="27583-155">Recibirá un mensaje de texto con el código de verificación.</span><span class="sxs-lookup"><span data-stu-id="27583-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="27583-156">Escríbalo y pulse **Enviar** .</span><span class="sxs-lookup"><span data-stu-id="27583-156">Enter it and tap **Submit**</span></span>

![Página comprobar el número de teléfono](2fa/_static/login2fa4.png)

<span data-ttu-id="27583-158">Si no recibe un mensaje de texto, consulte la página de registro de Twilio.</span><span class="sxs-lookup"><span data-stu-id="27583-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="27583-159">La vista administrar muestra que el número de teléfono se ha agregado correctamente.</span><span class="sxs-lookup"><span data-stu-id="27583-159">The Manage view shows your phone number was added successfully.</span></span>

![Vista de administración: número de teléfono agregado correctamente](2fa/_static/login2fa5.png)

* <span data-ttu-id="27583-161">Pulse **Habilitar** para habilitar la autenticación en dos fases.</span><span class="sxs-lookup"><span data-stu-id="27583-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Administrar la vista: habilitar la autenticación en dos fases](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="27583-163">Prueba de la autenticación en dos fases</span><span class="sxs-lookup"><span data-stu-id="27583-163">Test two-factor authentication</span></span>

* <span data-ttu-id="27583-164">Cierre la sesión.</span><span class="sxs-lookup"><span data-stu-id="27583-164">Log off.</span></span>

* <span data-ttu-id="27583-165">Inicie sesión.</span><span class="sxs-lookup"><span data-stu-id="27583-165">Log in.</span></span>

* <span data-ttu-id="27583-166">La cuenta de usuario ha habilitado la autenticación en dos fases, por lo que debe proporcionar el segundo factor de autenticación.</span><span class="sxs-lookup"><span data-stu-id="27583-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="27583-167">En este tutorial, ha habilitado la comprobación del teléfono.</span><span class="sxs-lookup"><span data-stu-id="27583-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="27583-168">Las plantillas integradas también permiten configurar el correo electrónico como segundo factor.</span><span class="sxs-lookup"><span data-stu-id="27583-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="27583-169">Puede configurar factores de segundo adicionales para la autenticación como códigos QR.</span><span class="sxs-lookup"><span data-stu-id="27583-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="27583-170">Pulse **submit (enviar**).</span><span class="sxs-lookup"><span data-stu-id="27583-170">Tap **Submit**.</span></span>

![Enviar vista de código de verificación](2fa/_static/login2fa7.png)

* <span data-ttu-id="27583-172">Escriba el código que se obtiene en el mensaje SMS.</span><span class="sxs-lookup"><span data-stu-id="27583-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="27583-173">Al hacer clic en la casilla **recordar este explorador** , no es necesario usar 2FA para iniciar sesión cuando se usa el mismo dispositivo y explorador.</span><span class="sxs-lookup"><span data-stu-id="27583-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="27583-174">Al habilitar 2FA y hacer clic en **recordar, este explorador** le proporcionará protección de 2FA segura a los usuarios malintencionados que intentan acceder a su cuenta, siempre y cuando no tengan acceso a su dispositivo.</span><span class="sxs-lookup"><span data-stu-id="27583-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="27583-175">Puede hacerlo en cualquier dispositivo privado que use con regularidad.</span><span class="sxs-lookup"><span data-stu-id="27583-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="27583-176">Al establecer la opción **recordar este explorador**, obtendrá la seguridad agregada de 2FA de los dispositivos que no use con regularidad y que le resulte más cómodo no tener que ir a través de 2FA en sus propios dispositivos.</span><span class="sxs-lookup"><span data-stu-id="27583-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Comprobar vista](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="27583-178">Bloqueo de cuenta para la protección frente a ataques por fuerza bruta</span><span class="sxs-lookup"><span data-stu-id="27583-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="27583-179">Se recomienda el bloqueo de cuentas con 2FA.</span><span class="sxs-lookup"><span data-stu-id="27583-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="27583-180">Una vez que un usuario inicia sesión a través de una cuenta local o de una cuenta de redes sociales, se almacenan todos los intentos fallidos en 2FA.</span><span class="sxs-lookup"><span data-stu-id="27583-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="27583-181">Si se alcanza el máximo de intentos de acceso incorrectos, el usuario se bloquea (valor predeterminado: 5 minutos de bloqueo después de 5 intentos de acceso incorrectos).</span><span class="sxs-lookup"><span data-stu-id="27583-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="27583-182">Una autenticación correcta restablece el número de intentos de acceso incorrectos y restablece el reloj.</span><span class="sxs-lookup"><span data-stu-id="27583-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="27583-183">El tiempo máximo de bloqueo y los intentos de acceso incorrectos se pueden establecer con [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) y [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="27583-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="27583-184">El siguiente código configura el bloqueo de cuentas durante 10 minutos después de 10 intentos de acceso incorrectos:</span><span class="sxs-lookup"><span data-stu-id="27583-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="27583-185">Confirme que [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) establece `lockoutOnFailure` en `true` :</span><span class="sxs-lookup"><span data-stu-id="27583-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
