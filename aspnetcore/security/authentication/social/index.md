---
title: Autenticación con Facebook, Google y proveedores externos en ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra cómo crear una aplicación de ASP.NET Core mediante OAuth 2.0 con proveedores de autenticación externos.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: f7bc7c6a14d014f8c3c283fb09718b9191b7b478
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035832"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Autenticación con Facebook, Google y proveedores externos en ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)

En este tutorial se muestra cómo crear una aplicación de ASP.NET Core que permita a los usuarios iniciar sesión mediante OAuth 2.0 con credenciales de proveedores de autenticación externos.

En las siguientes secciones se tratan los proveedores [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) y [Microsoft](xref:security/authentication/microsoft-logins), y usan el proyecto inicial creado en este artículo. Hay disponibles otros proveedores en paquetes de terceros como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) y [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

El hecho de permitir a los usuarios iniciar sesión con sus credenciales:

* resulta muy práctico para ellos;
* transfiere muchas de las complejidades de administrar el proceso de inicio de sesión a un tercero.

Para ver ejemplos de cómo los inicios de sesión de las redes sociales pueden controlar las conversiones del tráfico y de clientes, vea los casos prácticos de [Facebook](https://www.facebook.com/unsupportedbrowser) y [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Crear un proyecto de ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cree un nuevo proyecto.
* Seleccione **Aplicación web de ASP.NET Core** y **Siguiente**.
* Proporcione un **Nombre del proyecto** y confirme o cambie la **Ubicación**. Seleccione **Crear**.
* Seleccione la versión más reciente de ASP.NET Core en la lista desplegable (**ASP.NET Core {X.Y}** ) y, luego, **Aplicación web**.
* En **Autenticación**, seleccione **Cambiar** y establezca la autenticación en **Cuentas de usuario individuales**. Seleccione **Aceptar**.
* En la ventana **Crear una aplicación web ASP.NET Core**, seleccione **Crear**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Abra el terminal.  Para Visual Studio Code puede abrir el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.

* En Windows, ejecute el comando siguiente:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  En macOS y Linux, ejecute el siguiente comando:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * El comando `dotnet new` crea un proyecto de RazorPages en la carpeta *WebApp1*.
  * `-au Individual` crea el código para la autenticación individual.
  * `-uld` usa LocalDB, una versión ligera de SQL Server Express para Windows. Omita `-uld` para usar SQLite.
  * El comando `code` abre la carpeta *WebApp1* en una nueva instancia de Visual Studio Code.

---

## <a name="apply-migrations"></a>Aplicación de migraciones

* Ejecute la aplicación y seleccione el vínculo **Registrar**.
* Escriba el correo electrónico y la contraseña de la cuenta nueva y, luego, seleccione **Registrarse**.
* Siga estas instrucciones para aplicar las migraciones.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Uso de SecretManager para almacenar los tokens asignados por los proveedores de inicio de sesión

Los proveedores de inicio de sesión de las redes sociales asignan los tokens **Id. de aplicación** y **Secreto de la aplicación** durante el proceso de registro. La nomenclatura puede variar en función del proveedor. Estos tokens representan las credenciales que usa la aplicación para acceder a su API. Los tokens constituyen los "secretos de usuario" que se pueden vincular a la configuración de la aplicación con la ayuda del [Administrador de secretos](xref:security/app-secrets#secret-manager). Los secretos de usuario son una alternativa más segura al almacenamiento de los tokens en un archivo de configuración, como, por ejemplo, *appsettings.json* .

> [!IMPORTANT]
> Secret Manager solo está pensado para fines de desarrollo. Puede almacenar y proteger sus secretos de producción y pruebas de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).

Siga los pasos descritos en el tema [Ubicación de almacenamiento segura de secretos de la aplicación en el desarrollo de ASP.NET Core](xref:security/app-secrets) para almacenar los tokens asignados por cada uno de los siguientes proveedores de inicio de sesión.

## <a name="setup-login-providers-required-by-your-application"></a>Configuración de los proveedores de inicio de sesión requeridos por la aplicación

En los temas siguientes encontrará información para configurar la aplicación a fin de usar los proveedores correspondientes:

* Instrucciones para [Facebook](xref:security/authentication/facebook-logins)
* Instrucciones para [Twitter](xref:security/authentication/twitter-logins)
* Instrucciones para [Google](xref:security/authentication/google-logins)
* Instrucciones para [Microsoft](xref:security/authentication/microsoft-logins)
* Instrucciones para [otros proveedores](xref:security/authentication/otherlogins)

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Establecimiento opcional de contraseña

Si el registro se realiza mediante un proveedor de inicio de sesión externo, no se tiene ninguna contraseña en la aplicación. De esta forma no hace falta crear y recordar una contraseña para el sitio, aunque le hace depender del proveedor de inicio de sesión externo. Si el proveedor de inicio de sesión externo no está disponible, no podrá iniciar sesión en el sitio web.

Para crear una contraseña e iniciar sesión con el correo electrónico establecido durante el proceso de inicio de sesión con proveedores externos:

* Seleccione el vínculo **Hola, &lt;alias de correo electrónico&gt;** situado en la esquina superior derecha para ir a la vista **Administración**.

![Vista Administración de la aplicación web](index/_static/pass1a.png)

* Seleccione **Crear**.

![Página para establecer la contraseña](index/_static/pass2a.png)

* Establezca una contraseña válida. Podrá usarla para iniciar sesión con su correo electrónico.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [esta incidencia de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/10563) para obtener información sobre cómo personalizar los botones de inicio de sesión.
* En este artículo se introdujo la autenticación externa y se explicaron los requisitos previos necesarios para agregar inicios de sesión externos a la aplicación de ASP.NET Core.
* Páginas de referencia específicas del proveedor para configurar los inicios de sesión para los proveedores requeridos por la aplicación.
* Le recomendamos que conserve los datos adicionales sobre el usuario y sus tokens de acceso y actualización. Para obtener más información, vea <xref:security/authentication/social/additional-claims>.
