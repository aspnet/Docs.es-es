---
title: Configuración de inicio de sesión externo de Facebook en ASP.NET Core
author: rick-anderson
description: Tutorial con ejemplos de código que muestran la integración de la autenticación de usuarios de cuentas de Facebook en una aplicación de ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: ce0e7ad30c137562b74dc9fe5c53235e3599e575
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634363"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuración de inicio de sesión externo de Facebook en ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

En este tutorial con ejemplos de código se muestra cómo habilitar a los usuarios para que inicien sesión con su cuenta de Facebook mediante un proyecto de ejemplo ASP.NET Core 3,0 creado en la [página anterior](xref:security/authentication/social/index). Comenzaremos por crear un identificador de aplicación de Facebook siguiendo los [pasos oficiales](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Creación de la aplicación en Facebook

* Agregue el paquete NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al proyecto.

* Vaya a la página de la [aplicación de desarrolladores de Facebook](https://developers.facebook.com/apps/) e inicie sesión. Si aún no tiene una cuenta de Facebook, use el vínculo **suscribirse a Facebook** en la página de inicio de sesión para crear una.  Una vez que tenga una cuenta de Facebook, siga las instrucciones para registrarse como desarrollador de Facebook.

* En el menú **mis aplicaciones** , seleccione **crear aplicación** para crear un nuevo identificador de aplicación.

   ![El portal de Facebook para desarrolladores está abierto en Microsoft Edge](index/_static/FBMyApps.png)

* Rellene el formulario y pulse el botón **crear ID** . de aplicación.

  ![Crear un nuevo formulario de ID. de aplicación](index/_static/FBNewAppId.png)

* En la tarjeta nueva aplicación, seleccione **Agregar un producto**.  En la tarjeta de **Inicio de sesión de Facebook** , haga clic en **configurar** . 

  ![Página de configuración del producto](index/_static/FBProductSetup.png)

* El Asistente de **Inicio rápido** se inicia con **elegir una plataforma** como primera página. Omita el asistente por ahora; para ello, haga clic en el vínculo **configuración** de **Inicio de sesión de Facebook** en el menú de la parte inferior izquierda:

  ![Omitir Inicio rápido](index/_static/FBSkipQuickStart.png)

* Aparecerá la página Configuración de **OAuth de cliente** :

  ![Página de configuración de OAuth de cliente](index/_static/FBOAuthSetup.png)

* Escriba el URI de desarrollo con */signin-Facebook* anexado en el campo **URI de redirección de OAuth válido** (por ejemplo: `https://localhost:44320/signin-facebook` ). La autenticación de Facebook configurada más adelante en este tutorial administrará automáticamente las solicitudes en la ruta */signin-Facebook* para implementar el flujo de OAuth.

> [!NOTE]
> El URI */signin-Facebook* se establece como la devolución de llamada predeterminada del proveedor de autenticación de Facebook. Puede cambiar el URI de devolución de llamada predeterminado mientras configura el middleware de autenticación de Facebook a través de la propiedad heredada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la clase [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Haga clic en **Guardar cambios**.

* Haga clic en **configuración**  >  vínculo**básico** en el panel de navegación izquierdo.

  En esta página, tome nota de su `App ID` y su `App Secret` . En la siguiente sección, agregará ambos a la aplicación ASP.NET Core:

* Al implementar el sitio, debe volver a visitar la página de configuración de **Inicio de sesión de Facebook** y registrar un nuevo URI público.

## <a name="store-the-facebook-app-id-and-secret"></a>Almacenar el identificador y el secreto de la aplicación de Facebook

Almacene la configuración confidencial, como el identificador de la aplicación de Facebook y los valores de secreto con el [Administrador de secretos](xref:security/app-secrets). En este ejemplo, siga estos pasos:

1. Inicialice el proyecto para el almacenamiento de secretos según las instrucciones de [enable Secret Storage](xref:security/app-secrets#enable-secret-storage).
1. Almacene la configuración confidencial en el almacén secreto local con las claves secretas `Authentication:Facebook:AppId` y `Authentication:Facebook:AppSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configuración de la autenticación de Facebook

Agregue el servicio Facebook en el `ConfigureServices` método en el archivo *Startup.CS* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Inicio de sesión con Facebook

* Ejecute la aplicación y seleccione **iniciar sesión**. 
* En **usar otro servicio para iniciar sesión**, seleccione Facebook.
* Se le redirigirá a **Facebook** para la autenticación.
* Escriba sus credenciales de Facebook.
* Se le redirigirá de nuevo al sitio donde puede establecer el correo electrónico.

Ya ha iniciado sesión con sus credenciales de Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>ReAct para cancelar autorizar el inicio de sesión externo

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> puede proporcionar una ruta de acceso de redireccionamiento al agente de usuario cuando el usuario no aprueba la demanda de autorización solicitada.

El código siguiente establece `AccessDeniedPath` en `"/AccessDeniedPathInfo"` :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Se recomienda que la `AccessDeniedPath` página contenga la siguiente información:

*  Se canceló la autenticación remota.
* Esta aplicación requiere autenticación.
* Para volver a intentar iniciar sesión, seleccione el vínculo de inicio de sesión.

### <a name="test-accessdeniedpath"></a>Probar AccessDeniedPath

* Vaya a [Facebook.com](https://www.facebook.com/)
* Si ha iniciado sesión, debe cerrar la sesión.
* Ejecute la aplicación y seleccione el inicio de sesión de Facebook.
* Seleccione **no ahora**. Se le redirigirá a la `AccessDeniedPath` página especificada.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte la referencia de la API de [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obtener más información sobre las opciones de configuración admitidas por la autenticación de Facebook. Las opciones de configuración se pueden usar para:

* Solicitar información diferente sobre el usuario.
* Agregue argumentos de cadena de consulta para personalizar la experiencia de inicio de sesión.

## <a name="troubleshooting"></a>Solución de problemas

* **Solo ASP.net Core 2. x:** Si Identity no se configura llamando a `services.AddIdentity` en `ConfigureServices` , se *debe proporcionar la opción ' SignInScheme '* al intentar la autenticación. La plantilla de proyecto que se usa en este tutorial garantiza que esto se realiza.
* Si la base de datos del sitio no se ha creado aplicando la migración inicial, se producirá *un error en la operación de base de datos al procesar el error de solicitud* . Pulse **aplicar migraciones** para crear la base de datos y actualizar para continuar después del error.

## <a name="next-steps"></a>Pasos siguientes

* En este artículo se ha mostrado cómo puede realizar la autenticación con Facebook. Puede seguir un enfoque similar para autenticarse con otros proveedores mostrados en la [página anterior](xref:security/authentication/social/index).

* Una vez que publique el sitio web en la aplicación Web de Azure, debe restablecer el `AppSecret` en el portal para desarrolladores de Facebook.

* Establezca `Authentication:Facebook:AppId` y `Authentication:Facebook:AppSecret` como configuración de la aplicación en el Azure portal. El sistema de configuración está configurado para leer las claves de las variables de entorno.
