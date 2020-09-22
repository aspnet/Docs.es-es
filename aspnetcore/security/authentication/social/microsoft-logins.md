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
ms.openlocfilehash: 36341a0e439be57d7da4f787aa6103b92c624e96
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847590"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuración de inicio de sesión externo de cuenta de Microsoft con ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

Este ejemplo muestra cómo permitir a los usuarios iniciar sesión con su cuenta de Microsoft profesional, educativa o personal mediante el proyecto ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Creación de la aplicación en el portal para desarrolladores de Microsoft

* Agregue el paquete NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al proyecto.
* Vaya a la página [de registros de aplicaciones de Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) y cree o inicie sesión en un cuenta de Microsoft:

Si no tiene un cuenta de Microsoft, seleccione **crear uno**. Después de iniciar sesión, se le redirigirá a la página **registros de aplicaciones** :

* Seleccionar **nuevo registro**
* Escriba un **nombre**.
* Seleccione una opción para los **tipos de cuenta compatibles**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * El `MicrosoftAccount` paquete admite los registros de aplicaciones creados con las opciones "cuentas en cualquier directorio de la organización" o "cuentas de cualquier directorio de la organización y cuentas de Microsoft" de forma predeterminada.
  * Para usar otras opciones, establezca `AuthorizationEndpoint` y `TokenEndpoint` los miembros de `MicrosoftAccountOptions` se usan para inicializar la autenticación de la cuenta Microsoft en las direcciones URL que se muestran en la página **puntos de conexión** del registro de la aplicación después de crearla (disponible al hacer clic en puntos de conexión en la página **información general** ).
* En **URI de redirección**, escriba la dirección URL de desarrollo con `/signin-microsoft` anexado. Por ejemplo, `https://localhost:5001/signin-microsoft`. El esquema de autenticación de Microsoft configurado más adelante en este ejemplo administrará automáticamente las solicitudes en `/signin-microsoft` la ruta para implementar el flujo de OAuth.
* Seleccione **Registrar**.

### <a name="create-client-secret"></a>Creación de un secreto de cliente

* En el panel izquierdo, seleccione **Certificados y secretos**.
* En **secretos de cliente**, seleccione **nuevo secreto de cliente** .

  * Agregue una descripción para el secreto de cliente.
  * Seleccione el botón **Agregar**.

* En **secretos de cliente**, copie el valor del secreto de cliente.

El segmento URI `/signin-microsoft` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Microsoft. Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Microsoft a través de la propiedad [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Almacenar el identificador y el secreto de cliente de Microsoft

Almacene la configuración confidencial, como los valores de identificador de cliente y secreto de Microsoft, con el [Administrador de secretos](xref:security/app-secrets). En este ejemplo, siga estos pasos:

1. Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Microsoft:ClientId` y `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar la autenticación de la cuenta Microsoft

Agregue el servicio de la cuenta de Microsoft a `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Para obtener más información sobre las opciones de configuración admitidas por la autenticación de la cuenta de Microsoft, consulte la referencia de la API de [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Se puede usar para solicitar información diferente sobre el usuario.

## <a name="sign-in-with-microsoft-account"></a>Iniciar sesión con Microsoft cuenta

Ejecute la aplicación y haga clic en **iniciar sesión**. Aparece una opción para iniciar sesión con Microsoft. Al hacer clic en Microsoft, se le redirigirá a Microsoft para la autenticación. Después de iniciar sesión con su cuenta de Microsoft, se le pedirá que permita que la aplicación acceda a su información:

Puntee en **sí** y se le redirigirá de nuevo al sitio web donde puede establecer el correo electrónico.

Ya ha iniciado sesión con sus credenciales de Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solución de problemas

* Si el proveedor de la cuenta de Microsoft le redirige a una página de error de inicio de sesión, tenga en cuenta los parámetros de cadena de consulta título y descripción del error directamente después de `#` (hashtag) en el URI.

  Aunque el mensaje de error parece indicar un problema con la autenticación de Microsoft, la causa más común es que el URI de la aplicación no coincida con ninguno de los **URI de redirección** especificados para la plataforma **Web** .
* Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación. La plantilla de proyecto utilizada en este ejemplo garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* . Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.

## <a name="next-steps"></a>Pasos siguientes

* En este artículo se ha mostrado cómo puede autenticarse con Microsoft. Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).

* Una vez que publique el sitio web en la aplicación Web de Azure, cree un nuevo secreto de cliente en el portal para desarrolladores de Microsoft.

* Establezca `Authentication:Microsoft:ClientId` y `Authentication:Microsoft:ClientSecret` como configuración de la aplicación en el Azure portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
