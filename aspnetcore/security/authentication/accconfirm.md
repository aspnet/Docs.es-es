---
title: Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación ASP.NET Core con confirmación por correo electrónico y restablecimiento de contraseña.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: d6ea37ceb83ffbaa94187e0c541c79428594e4b4
ms.sourcegitcommit: 2039e60eb7b482da8298f82dcd5eda27cf747f32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88906454"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)y [Joe Audette](https://twitter.com/joeaudette)

En este tutorial se muestra cómo crear una aplicación ASP.NET Core con confirmación de correo electrónico y restablecimiento de contraseña. Este tutorial **no** es un tema de inicio. Debe estar familiarizado con:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Autenticación](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

[SDK de .NET Core 3.0 o versiones posteriores](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Crear y probar una aplicación web con autenticación

Ejecute los siguientes comandos para crear una aplicación web con autenticación.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario. Una vez registrado, se le redirigirá a la `/Identity/Account/RegisterConfirmation` página a que contiene un vínculo para simular la confirmación de correo electrónico:

* Seleccione el vínculo `Click here to confirm your account`.
* Seleccione el vínculo de **Inicio de sesión** e inicie sesión con las mismas credenciales.
* Seleccione el `Hello YourEmail@provider.com!` vínculo, que le redirigirá a la `/Identity/Account/Manage/PersonalData` página.
* Seleccione la pestaña **datos personales** de la izquierda y, a continuación, seleccione **eliminar**.

### <a name="configure-an-email-provider"></a>Configuración de un proveedor de correo electrónico

En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico. Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico. Puede usar otros proveedores de correo electrónico. Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico. SMTP es difícil de proteger y configurar correctamente.

Es posible que la cuenta de SendGrid necesite [Agregar un remitente](https://sendgrid.com/docs/ui/sending-email/senders/).

Cree una clase para capturar la clave de correo electrónico segura. Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configuración de secretos de usuario de SendGrid

Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets). Por ejemplo:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

En Windows, el administrador de secretos almacena pares de clave/valor en un *secrets.jsen* el archivo en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.

No se cifra el contenido de la *secrets.jsen* el archivo. En el marcado siguiente se muestra el *secrets.jsen* el archivo. Se ha `SendGridKey` quitado el valor.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalación de SendGrid

En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.

Instale el `SendGrid` paquete NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del administrador de paquetes, escriba el siguiente comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

En la consola de, escriba el siguiente comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.

### <a name="implement-iemailsender"></a>Implementación de IEmailSender

Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar el inicio para admitir correo electrónico

Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :

* Agregue `EmailSender` como un servicio transitorio.
* Registre la `AuthMessageSenderOptions` instancia de configuración.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>Scaffold RegisterConfirmation

Siga las instrucciones de [scaffolding Identity ](xref:security/authentication/scaffold-identity) y scaffold `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Registro, confirmación de correo electrónico y restablecimiento de contraseña

Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.

* Ejecutar la aplicación y registrar un nuevo usuario
* Compruebe su correo electrónico para el vínculo de confirmación de la cuenta. Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.
* Haga clic en el vínculo para confirmar el correo electrónico.
* Inicie sesión con su correo electrónico y contraseña.
* Cierre la sesión.

### <a name="test-password-reset"></a>Restablecimiento de la contraseña de prueba

* Si ha iniciado sesión, seleccione **Logout**.
* Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .
* Escriba el correo electrónico que usó para registrar la cuenta.
* Se envía un correo electrónico con un vínculo para restablecer la contraseña. Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña. Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.

<a name="resend"></a>

## <a name="resend-email-confirmation"></a>Confirmación de reenvío de correo electrónico

En ASP.NET Core 5,0 y versiones posteriores, seleccione el vínculo **reenviar confirmación de correo electrónico** en la página de **Inicio de sesión** .

### <a name="change-email-and-activity-timeout"></a>Cambiar el tiempo de espera del correo electrónico y la actividad

El tiempo de espera de inactividad predeterminado es de 14 días. El código siguiente establece el tiempo de espera de inactividad en 5 días:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Cambiar todas las vigencias de los tokens de protección de datos

El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.

### <a name="change-the-email-token-lifespan"></a>Cambiar la duración del token de correo electrónico

La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). En esta sección se muestra cómo cambiar la duración del token de correo electrónico.

Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Agregue el proveedor personalizado al contenedor de servicio:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a>Depurar correo electrónico

Si no consigue que el correo electrónico funcione:

* Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.
* Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .
* Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Compruebe la carpeta de correo no deseado.
* Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).
* Intente enviar a diferentes cuentas de correo electrónico.

**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo. Si publica la aplicación en Azure, establezca los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure. El sistema de configuración está configurado para leer las claves de las variables de entorno.

## <a name="combine-social-and-local-login-accounts"></a>Combinación de cuentas de inicio de sesión sociales y locales

Para completar esta sección, primero debe habilitar un proveedor de autenticación externo. Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).

Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico. En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

Haga clic en el vínculo **administrar** . Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.

![Administrar vista](accconfirm/_static/manage.png)

Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación. En la imagen siguiente, Facebook es el proveedor de autenticación externo:

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

Las dos cuentas se han combinado. Puede iniciar sesión con cualquiera de las dos cuentas. Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios

La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes. Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas. Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:

* Actualice la base de datos para marcar todos los usuarios existentes como confirmados.
* Confirme los usuarios existentes. Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

[SDK de .NET Core 2,2 o posterior](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a>Creación de una aplicación web y scaffolding Identity

Ejecute los siguientes comandos para crear una aplicación web con autenticación.

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
> Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding. Una `InputModel` `Password` propiedad se encuentra en el `Areas/Identity/Pages/Account/Register.cshtml.cs` archivo después de la técnica scaffolding Identity .

## <a name="test-new-user-registration"></a>Prueba del nuevo registro de usuario

Ejecute la aplicación, seleccione el vínculo **registrar** y registre un usuario. En este momento, la única validación en el correo electrónico es con el [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo. Después de enviar el registro, ha iniciado sesión en la aplicación. Más adelante en el tutorial, se actualiza el código para que los nuevos usuarios no puedan iniciar sesión hasta que se valide su correo electrónico.

[!INCLUDE[](~/includes/view-identity-db.md)]

Tenga en cuenta que el campo de la tabla `EmailConfirmed` es `False` .

Es posible que desee usar este correo electrónico de nuevo en el paso siguiente cuando la aplicación envíe un correo electrónico de confirmación. Haga clic con el botón derecho en la fila y seleccione **eliminar**. Al eliminar el alias de correo electrónico, se facilitan los pasos siguientes.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Requerir confirmación de correo electrónico

Se recomienda confirmar el correo electrónico de un nuevo registro de usuario. La confirmación por correo electrónico ayuda a comprobar que no está suplantando a otra persona (es decir, que no se han registrado con el correo electrónico de otro usuario). Supongamos que tiene un foro de discusión y desea evitar que " yli@example.com " se registre como " nolivetto@contoso.com ". Sin confirmación por correo electrónico, " nolivetto@contoso.com " podría recibir correo electrónico no deseado de la aplicación. Supongamos que el usuario se registró accidentalmente como " ylo@example.com " y no detectó la falta de ortografía de "Yli". No podrán usar la recuperación de contraseña porque la aplicación no tiene el correo electrónico correcto. La confirmación por correo electrónico proporciona una protección limitada de bots. La confirmación por correo electrónico no proporciona protección contra usuarios malintencionados con muchas cuentas de correo electrónico.

Por lo general, querrá evitar que los nuevos usuarios publiquen datos en el sitio Web antes de que tengan un correo electrónico confirmado.

Actualización `Startup.ConfigureServices`  para requerir un correo electrónico confirmado:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` impide que los usuarios registrados inicien sesión hasta que se confirme su correo electrónico.

### <a name="configure-email-provider"></a>Configurar proveedor de correo electrónico

En este tutorial, se usa [SendGrid](https://sendgrid.com) para enviar correo electrónico. Necesita una cuenta y una clave de SendGrid para enviar el correo electrónico. Puede usar otros proveedores de correo electrónico. ASP.NET Core 2. x incluye `System.Net.Mail` , que le permite enviar correo electrónico desde su aplicación. Se recomienda usar SendGrid u otro servicio de correo electrónico para enviar correo electrónico. SMTP es difícil de proteger y configurar correctamente.

Cree una clase para capturar la clave de correo electrónico segura. Para este ejemplo, cree *Services/AuthMessageSenderOptions. CS*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configuración de secretos de usuario de SendGrid

Establezca `SendGridUser` y `SendGridKey` con la [herramienta de administrador de secretos](xref:security/app-secrets). Por ejemplo:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

En Windows, el administrador de secretos almacena pares de clave/valor en un *secrets.jsen* el archivo en el `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directorio.

No se cifra el contenido de la *secrets.jsen* el archivo. En el marcado siguiente se muestra el *secrets.jsen* el archivo. Se ha `SendGridKey` quitado el valor.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obtener más información, vea el [patrón de opciones](xref:fundamentals/configuration/options) y la [configuración](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalación de SendGrid

En este tutorial se muestra cómo agregar notificaciones por correo electrónico a través de [SendGrid](https://sendgrid.com/), pero puede enviar correo electrónico mediante SMTP y otros mecanismos.

Instale el `SendGrid` paquete NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del administrador de paquetes, escriba el siguiente comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

En la consola de, escriba el siguiente comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introducción a [SendGrid gratis](https://sendgrid.com/free/) para registrarse para obtener una cuenta gratuita de SendGrid.

### <a name="implement-iemailsender"></a>Implementación de IEmailSender

Para implementar `IEmailSender` , cree *Services/EmailSender. CS* con código similar al siguiente:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar el inicio para admitir correo electrónico

Agregue el código siguiente al `ConfigureServices` método en el archivo *Startup.CS* :

* Agregue `EmailSender` como un servicio transitorio.
* Registre la `AuthMessageSenderOptions` instancia de configuración.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Habilitar la confirmación de la cuenta y la recuperación de la contraseña

La plantilla tiene el código para la confirmación de la cuenta y la recuperación de la contraseña. Busque el `OnPostAsync` método en *areas/ Identity /pages/Account/Register.cshtml.CS*.

Evite que los usuarios recién registrados inicien sesión automáticamente al comentar la siguiente línea:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

El método complete se muestra con la línea resaltada:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registro, confirmación de correo electrónico y restablecimiento de contraseña

Ejecute la aplicación web y pruebe el flujo de recuperación de la contraseña y la confirmación de la cuenta.

* Ejecutar la aplicación y registrar un nuevo usuario
* Compruebe su correo electrónico para el vínculo de confirmación de la cuenta. Consulte [depurar correo electrónico](#debug) si no recibe el correo electrónico.
* Haga clic en el vínculo para confirmar el correo electrónico.
* Inicie sesión con su correo electrónico y contraseña.
* Cierre la sesión.

### <a name="view-the-manage-page"></a>Ver la página administrar

Seleccione el nombre de usuario en el explorador: ![ ventana del explorador con el nombre de usuario](accconfirm/_static/un.png)

La página Administrar se muestra con la pestaña **perfil** seleccionada. El **correo electrónico** muestra una casilla que indica que se ha confirmado el correo electrónico.

### <a name="test-password-reset"></a>Restablecimiento de la contraseña de prueba

* Si ha iniciado sesión, seleccione **Logout**.
* Seleccione el vínculo **iniciar sesión** y seleccione el vínculo **¿olvidó su contraseña?** .
* Escriba el correo electrónico que usó para registrar la cuenta.
* Se envía un correo electrónico con un vínculo para restablecer la contraseña. Compruebe su correo electrónico y haga clic en el vínculo para restablecer la contraseña. Una vez que la contraseña se haya restablecido correctamente, puede iniciar sesión con el correo electrónico y la nueva contraseña.

## <a name="change-email-and-activity-timeout"></a>Cambiar el tiempo de espera del correo electrónico y la actividad

El tiempo de espera de inactividad predeterminado es de 14 días. El código siguiente establece el tiempo de espera de inactividad en 5 días:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Cambiar todas las vigencias de los tokens de protección de datos

El siguiente código cambia el tiempo de espera de todos los tokens de protección de datos a 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Los Identity tokens de usuario integrados (consulte [AspNetCore/src/ Identity /extensions.Core/src/TokenOptions.CS](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tienen un [tiempo de espera](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)de un día.

### <a name="change-the-email-token-lifespan"></a>Cambiar la duración del token de correo electrónico

La duración predeterminada del token de [los Identity tokens de usuario](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) es de [un día](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). En esta sección se muestra cómo cambiar la duración del token de correo electrónico.

Agregue un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado y <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Agregue el proveedor personalizado al contenedor de servicio:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Confirmación de reenvío de correo electrónico

Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar correo electrónico

Si no consigue que el correo electrónico funcione:

* Establezca un punto de interrupción en `EmailSender.Execute` para comprobar que `SendGridClient.SendEmailAsync` se llama a.
* Cree una [aplicación de consola para enviar correo electrónico](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mediante código similar a `EmailSender.Execute` .
* Revise la página [actividad de correo electrónico](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Compruebe la carpeta de correo no deseado.
* Pruebe otro alias de correo electrónico en un proveedor de correo electrónico diferente (Microsoft, Yahoo, gmail, etc.).
* Intente enviar a diferentes cuentas de correo electrónico.

**Un procedimiento** recomendado de seguridad es **no** usar secretos de producción en pruebas y desarrollo. Si publica la aplicación en Azure, puede establecer los secretos de SendGrid como configuración de la aplicación en el portal de aplicaciones Web de Azure. El sistema de configuración está configurado para leer las claves de las variables de entorno.

## <a name="combine-social-and-local-login-accounts"></a>Combinación de cuentas de inicio de sesión sociales y locales

Para completar esta sección, primero debe habilitar un proveedor de autenticación externo. Consulte la [autenticación de Facebook, Google y proveedores externos](xref:security/authentication/social/index).

Para combinar cuentas locales y sociales, haga clic en el vínculo de correo electrónico. En la siguiente secuencia, " RickAndMSFT@gmail.com " se crea primero como un inicio de sesión local; sin embargo, puede crear primero la cuenta como un inicio de sesión social y luego agregar un inicio de sesión local.

![Aplicación web: RickAndMSFT@gmail.com usuario autenticado](accconfirm/_static/rick.png)

Haga clic en el vínculo **administrar** . Observe los 0 externos (inicios de sesión sociales) asociados a esta cuenta.

![Administrar vista](accconfirm/_static/manage.png)

Haga clic en el vínculo a otro servicio de inicio de sesión y acepte las solicitudes de la aplicación. En la imagen siguiente, Facebook es el proveedor de autenticación externo:

![Administrar la vista de inicios de sesión externos enumerando Facebook](accconfirm/_static/fb.png)

Las dos cuentas se han combinado. Puede iniciar sesión con cualquiera de las dos cuentas. Es posible que desee que los usuarios agreguen cuentas locales en caso de que su servicio de autenticación de inicio de sesión social esté inactivo, o más probabilidades de que hayan perdido el acceso a su cuenta de redes sociales.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar la confirmación de la cuenta después de que un sitio tenga usuarios

La habilitación de la confirmación de cuenta en un sitio con usuarios bloquea a todos los usuarios existentes. Los usuarios existentes están bloqueados porque sus cuentas no están confirmadas. Para solucionar el bloqueo de usuario existente, use uno de los métodos siguientes:

* Actualice la base de datos para marcar todos los usuarios existentes como confirmados.
* Confirme los usuarios existentes. Por ejemplo, enviar por lotes correos electrónicos con vínculos de confirmación.

::: moniker-end
