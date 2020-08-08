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
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Autenticación en dos fases con SMS en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Suiza-desarrolladores](https://github.com/Swiss-Devs)

>[!WARNING]
> Las aplicaciones de autenticación en dos fases (2FA), con un algoritmo de contraseña de un solo uso (TOTP), son el enfoque recomendado del sector para 2FA. se prefiere 2FA con TOTP a SMS 2FA. Para obtener más información, consulte [habilitación de la generación de código QR para aplicaciones TOTP Authenticator en ASP.net Core](xref:security/authentication/identity-enable-qrcodes) para ASP.net Core 2,0 y versiones posteriores.

En este tutorial se muestra cómo configurar la autenticación en dos fases (2FA) mediante SMS. Se proporcionan instrucciones para [Twilio](https://www.twilio.com/) y [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), pero puede usar cualquier otro proveedor de SMS. Se recomienda completar la [confirmación de la cuenta y la recuperación de la contraseña antes de](xref:security/authentication/accconfirm) iniciar este tutorial.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Cómo descargar](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Crear un nuevo proyecto de ASP.NET Core

Cree una nueva aplicación Web de ASP.NET Core denominada `Web2FA` con cuentas de usuario individuales. Siga las instrucciones de <xref:security/enforcing-ssl> para configurar y requerir https.

### <a name="create-an-sms-account"></a>Crear una cuenta de SMS

Cree una cuenta de SMS, por ejemplo, de [Twilio](https://www.twilio.com/) o [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Grabe las credenciales de autenticación (para Twilio: accountSid y authToken, para ASPSMS: Userkey y password).

#### <a name="figuring-out-sms-provider-credentials"></a>Determinar las credenciales del proveedor de SMS

**Twilio**

En la pestaña panel de la cuenta de Twilio, copie el SID de la **cuenta** y el **token de autenticación**.

**ASPSMS:**

En la configuración de la cuenta, vaya a **Userkey** y cópiela junto con la **contraseña**.

Más adelante almacenaremos estos valores en con la herramienta de administrador de secretos dentro de las claves `SMSAccountIdentification` y `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>Especificar SenderID/originador

**Twilio:** En la pestaña números, copie el **número de teléfono**de Twilio.

**ASPSMS:** En el menú desbloquear orígenes, desbloquee uno o más originadores o elija un originador alfanumérico (no admitido por todas las redes).

Más adelante se almacenará este valor con la herramienta de administrador de secretos dentro de la clave `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>Proporcionar credenciales para el servicio de SMS

Usaremos el [patrón de opciones](xref:fundamentals/configuration/options) para tener acceso a la configuración de clave y cuenta de usuario.

* Cree una clase para capturar la clave segura de SMS. En este ejemplo, la `SMSoptions` clase se crea en el archivo *Services/SMSoptions. CS* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Establezca `SMSAccountIdentification` `SMSAccountPassword` y `SMSAccountFrom` con la [herramienta de administrador de secretos](xref:security/app-secrets). Por ejemplo:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Agregue el paquete NuGet para el proveedor de SMS. En la consola del administrador de paquetes (PMC), ejecute:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Agregue código en el archivo *Services/MessageServices. CS* para habilitar SMS. Use la sección Twilio o ASPSMS:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Configurar el inicio para usar`SMSoptions`

Agregue `SMSoptions` al contenedor de servicios en el `ConfigureServices` método en *Startup.CS*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Habilitación de la autenticación en dos fases

Abra el archivo de vista *views/Manage/index. cshtml* Razor y quite los caracteres de comentario (por lo que no hay marcado como comentario).

## <a name="log-in-with-two-factor-authentication"></a>Inicio de sesión con la autenticación en dos fases

* Ejecutar la aplicación y registrar un nuevo usuario

![Vista de registro de aplicación web abierta en Microsoft Edge](2fa/_static/login2fa1.png)

* Puntee en el nombre de usuario, que activa el `Index` método de acción en el controlador de administración. A continuación, puntee en el vínculo número de teléfono para **Agregar** .

![Administrar vista: Pulse el vínculo "agregar".](2fa/_static/login2fa2.png)

* Agregue un número de teléfono que recibirá el código de verificación y pulse **Enviar código de verificación**.

![Página Agregar número de teléfono](2fa/_static/login2fa3.png)

* Recibirá un mensaje de texto con el código de verificación. Escríbalo y pulse **Enviar** .

![Página comprobar el número de teléfono](2fa/_static/login2fa4.png)

Si no recibe un mensaje de texto, consulte la página de registro de Twilio.

* La vista administrar muestra que el número de teléfono se ha agregado correctamente.

![Vista de administración: número de teléfono agregado correctamente](2fa/_static/login2fa5.png)

* Pulse **Habilitar** para habilitar la autenticación en dos fases.

![Administrar la vista: habilitar la autenticación en dos fases](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Prueba de la autenticación en dos fases

* Cierre la sesión.

* Inicie sesión.

* La cuenta de usuario ha habilitado la autenticación en dos fases, por lo que debe proporcionar el segundo factor de autenticación. En este tutorial, ha habilitado la comprobación del teléfono. Las plantillas integradas también permiten configurar el correo electrónico como segundo factor. Puede configurar factores de segundo adicionales para la autenticación como códigos QR. Pulse **submit (enviar**).

![Enviar vista de código de verificación](2fa/_static/login2fa7.png)

* Escriba el código que se obtiene en el mensaje SMS.

* Al hacer clic en la casilla **recordar este explorador** , no es necesario usar 2FA para iniciar sesión cuando se usa el mismo dispositivo y explorador. Al habilitar 2FA y hacer clic en **recordar, este explorador** le proporcionará protección de 2FA segura a los usuarios malintencionados que intentan acceder a su cuenta, siempre y cuando no tengan acceso a su dispositivo. Puede hacerlo en cualquier dispositivo privado que use con regularidad. Al establecer la opción **recordar este explorador**, obtendrá la seguridad agregada de 2FA de los dispositivos que no use con regularidad y que le resulte más cómodo no tener que ir a través de 2FA en sus propios dispositivos.

![Comprobar vista](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Bloqueo de cuenta para la protección frente a ataques por fuerza bruta

Se recomienda el bloqueo de cuentas con 2FA. Una vez que un usuario inicia sesión a través de una cuenta local o de una cuenta de redes sociales, se almacenan todos los intentos fallidos en 2FA. Si se alcanza el máximo de intentos de acceso incorrectos, el usuario se bloquea (valor predeterminado: 5 minutos de bloqueo después de 5 intentos de acceso incorrectos). Una autenticación correcta restablece el número de intentos de acceso incorrectos y restablece el reloj. El tiempo máximo de bloqueo y los intentos de acceso incorrectos se pueden establecer con [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) y [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). El siguiente código configura el bloqueo de cuentas durante 10 minutos después de 10 intentos de acceso incorrectos:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Confirme que [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) establece `lockoutOnFailure` en `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
