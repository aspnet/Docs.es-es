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
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuración de inicio de sesión externo de Google en ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

En este tutorial se muestra cómo permitir que los usuarios inicien sesión con su cuenta de Google mediante el proyecto ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Creación de un proyecto y un identificador de cliente de la consola de API de Google

* Agregue el paquete de NuGet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) a la aplicación.
* Siga las instrucciones de [integración de google Sign-In en la aplicación web](https://developers.google.com/identity/sign-in/web/sign-in) (documentación de Google).
* En la página **credenciales** de la [consola de Google](https://console.developers.google.com/apis/credentials), seleccione **crear credenciales**  >  **ID**. de cliente de OAuth.
* En el cuadro de diálogo **tipo de aplicación** , seleccione **aplicación web**. Proporcione un **nombre** para la aplicación.
* En la sección **URI de redireccionamiento autorizados** , seleccione **Agregar URI** para establecer el URI de redirección. Ejemplo de URI de redireccionamiento: `https://localhost:{PORT}/signin-google` , donde el `{PORT}` marcador de posición es el puerto de la aplicación.
* Seleccione el botón **crear** .
* Guarde el **identificador de cliente** y el **secreto de cliente** para su uso en la configuración de la aplicación.
* Al implementar el sitio, puede:
  * Actualice el URI de redirección de la aplicación en la **consola de Google** para el URI de redireccionamiento implementado de la aplicación.
  * Cree un nuevo registro de la API de Google en la **consola de Google** para la aplicación de producción con su URI de redirección de producción.

## <a name="store-the-google-client-id-and-secret"></a>Almacenar el identificador y el secreto de cliente de Google

Almacenar valores confidenciales como el ID. de cliente de Google y los valores de secreto con el [Administrador de secretos](xref:security/app-secrets). En este ejemplo, siga estos pasos:

1. Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Google:ClientId` y `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Puede administrar las credenciales de API y el uso en la [consola de API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configuración de la autenticación de Google

Agregue el servicio de Google a `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Inicio de sesión con Google

* Ejecute la aplicación y haga clic en **iniciar sesión**. Aparece una opción para iniciar sesión con Google.
* Haga clic en el botón **Google** , que redirige a Google para la autenticación.
* Después de escribir las credenciales de Google, se le redirigirá al sitio Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> referencia de la API para obtener más información sobre las opciones de configuración admitidas por la autenticación de Google. Se puede usar para solicitar información diferente sobre el usuario.

## <a name="change-the-default-callback-uri"></a>Cambiar el URI de devolución de llamada predeterminado

El segmento URI `/signin-google` se establece como la devolución de llamada predeterminada del proveedor de autenticación de Google. Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Google a través de la propiedad heredada <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> ) de la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> clase.

## <a name="troubleshooting"></a>Solución de problemas

* Si el inicio de sesión no funciona y no recibe ningún error, cambie al modo de desarrollo para que el problema sea más fácil de depurar.
* Si Identity no se configura mediante una llamada a `services.AddIdentity` en `ConfigureServices` , *se debe proporcionar la opción ' SignInScheme '* al intentar autenticar los resultados. La plantilla de proyecto que se usa en este tutorial garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* . Seleccione **aplicar migraciones** para crear la base de datos y actualice la página para continuar después del error.

## <a name="next-steps"></a>Pasos siguientes

* En este artículo se ha mostrado cómo puede realizar la autenticación con Google. Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).
* Una vez que publique la aplicación en Azure, restablezca `ClientSecret` en la consola de la API de Google.
* Establezca `Authentication:Google:ClientId` y `Authentication:Google:ClientSecret` como configuración de la aplicación en el Azure portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
