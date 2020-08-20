---
title: Configuración de inicio de sesión externo de Twitter con ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra la integración de la autenticación de usuarios de cuentas de Twitter en una aplicación ASP.NET Core existente.
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: da0c5579b0828aee7f1c78ec7f5731db50151e90
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634064"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuración de inicio de sesión externo de Twitter con ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

Este ejemplo muestra cómo permitir a los usuarios [iniciar sesión con su cuenta de Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) mediante un proyecto de ejemplo ASP.net Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Creación de la aplicación en Twitter

* Agregue el paquete de NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al proyecto.

* Vaya a [https://apps.twitter.com/](https://apps.twitter.com/) e inicie sesión. Si aún no tiene una cuenta de Twitter, use el vínculo **[Regístrese ahora](https://twitter.com/signup)** para crear una.

* Seleccione **crear una aplicación**. Rellene el **nombre**de la aplicación, la descripción de la **aplicación** y el URI del **sitio web** público (esto puede ser temporal hasta que registre el nombre de dominio):

* Active la casilla situada junto a **Habilitar el inicio de sesión con Twitter** .

* Microsoft. AspNetCore.Identity requiere que los usuarios tengan una dirección de correo electrónico de forma predeterminada. Vaya a la pestaña **permisos** , haga clic en el botón **Editar** y active la casilla situada junto a **solicitar dirección de correo electrónico de los usuarios**.

* Escriba el URI de desarrollo con `/signin-twitter` anexado en el campo **URL de devolución de llamada** (por ejemplo: `https://webapp128.azurewebsites.net/signin-twitter` ). El esquema de autenticación de Twitter configurado más adelante en este ejemplo administrará automáticamente las solicitudes de `/signin-twitter` la ruta para implementar el flujo de OAuth.

  > [!NOTE]
  > El segmento URI `/signin-twitter` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Twitter. Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Twitter a través de la propiedad heredada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la clase [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Rellene el resto del formulario y seleccione **crear**. Se muestran los nuevos detalles de la aplicación:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Almacenar la clave y el secreto de la API de consumidor de Twitter

Almacene la configuración confidencial, como la clave de API de consumidor de Twitter y el secreto con el [Administrador de secretos](xref:security/app-secrets). En este ejemplo, siga estos pasos:

1. Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves de secretos `Authentication:Twitter:ConsumerKey` y `Authentication:Twitter:ConsumerSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Estos tokens se pueden encontrar en la pestaña **claves y tokens de acceso** después de crear una nueva aplicación de Twitter:

## <a name="configure-twitter-authentication"></a>Configuración de la autenticación de Twitter

Agregue el servicio Twitter en el `ConfigureServices` método en el archivo *Startup.CS* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte la referencia de la API de [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para más información sobre las opciones de configuración admitidas por la autenticación de Twitter. Se puede usar para solicitar información diferente sobre el usuario.

## <a name="sign-in-with-twitter"></a>Inicio de sesión con Twitter

Ejecute la aplicación y seleccione **iniciar sesión**. Aparece una opción para iniciar sesión con Twitter:

Al hacer clic en **Twitter** , se redirige a Twitter para la autenticación:

Después de escribir sus credenciales de Twitter, se le redirigirá al sitio web donde puede establecer el correo electrónico.

Ya ha iniciado sesión con sus credenciales de Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Solución de problemas

* **Solo ASP.net Core 2. x:** Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación. La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* . Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.

## <a name="next-steps"></a>Pasos siguientes

* En este artículo se ha mostrado cómo puede realizar la autenticación con Twitter. Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).

* Una vez que publique el sitio web en la aplicación Web de Azure, debe restablecer el `ConsumerSecret` en el portal para desarrolladores de Twitter.

* Establezca `Authentication:Twitter:ConsumerKey` y `Authentication:Twitter:ConsumerSecret` como configuración de la aplicación en el Azure portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
