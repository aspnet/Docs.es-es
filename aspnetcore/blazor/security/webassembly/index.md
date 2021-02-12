---
title: Protección de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Obtenga información sobre cómo proteger aplicaciones WebAssemlby de Blazor como aplicaciones de página única (SPA).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: fc2ebae6e88e312aafec790229f978c3130e64de
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106692"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Protección de ASP.NET Core Blazor WebAssembly

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Las aplicaciones de Blazor WebAssembly se protegen de la misma manera que las aplicaciones de página única (SPA). Hay varios métodos para autenticar a los usuarios en las SPA, pero el enfoque más común y completo consiste en usar una implementación basada en el [protocolo OAuth 2.0](https://oauth.net/), como [OpenID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticación

Blazor WebAssembly permite autenticar y autorizar las aplicaciones mediante OIDC a través de la biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). La biblioteca proporciona un conjunto de primitivas para la autenticación sin problemas en back-ends de ASP.NET Core. La biblioteca integra ASP.NET Core Identity con compatibilidad con la autorización de API basada en [Identity Server](https://identityserver.io/). La biblioteca puede realizar la autenticación sobre cualquier proveedor de Identity (IP) de terceros que admita OIDC, que se denominan proveedores de OpenID (OP).

La compatibilidad con la autenticación en Blazor WebAssembly se basa en la biblioteca `oidc-client.js`, que se usa para administrar los detalles del protocolo de autenticación subyacente.

Existen otras opciones para la autenticación de las SPA, como el uso de cookies de SameSite. Sin embargo, el diseño de ingeniería de Blazor WebAssembly se limita a OAuth y OIDC como mejor opción para la autenticación en las aplicaciones de Blazor WebAssembly. Se ha elegido la [autenticación basada en tokens](xref:security/anti-request-forgery#token-based-authentication) basada en [JSON Web Token (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) antes que la [autenticación basada en cookie](xref:security/anti-request-forgery#cookie-based-authentication) por razones funcionales y de seguridad:

* El uso de un protocolo basado en tokens ofrece una superficie expuesta a ataques más pequeña, ya que los tokens no se envían en todas las solicitudes.
* Los puntos de conexión de servidor no requieren protección contra la [Falsificación de solicitudes entre sitios (CSRF)](xref:security/anti-request-forgery), ya que los tokens se envían de forma explícita. Esto permite hospedar aplicaciones de Blazor WebAssembly junto con aplicaciones de páginas de Razor o de MVC.
* Los tokens tienen permisos más restringidos que las cookies. Por ejemplo, los tokens no se pueden usar para administrar la cuenta de usuario o cambiar su contraseña a menos que esa funcionalidad se implemente de forma explícita.
* Los tokens tienen una duración corta, una hora de forma predeterminada, lo que limita la ventana de ataque. Los tokens también se pueden revocar en cualquier momento.
* Los JWT independientes ofrecen garantías al cliente y al servidor sobre el proceso de autenticación. Por ejemplo, un cliente tiene los medios para detectar y validar que los tokens que recibe son legítimos y se han emitido como parte de un proceso de autenticación determinado. Si un tercero intenta cambiar un token en medio del proceso de autenticación, el cliente puede detectar el token cambiado y evitar usarlo.
* Los tokens con OAuth y OIDC no se basan en que el agente de usuario se comporte correctamente para asegurarse de que la aplicación sea segura.
* Los protocolos basados en tokens, como OAuth y OIDC, permiten autenticar y autorizar aplicaciones independientes y hospedadas con el mismo conjunto de características de seguridad.

## <a name="authentication-process-with-oidc"></a>Proceso de autenticación con OIDC

La biblioteca [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ofrece varias primitivas para implementar la autenticación y autorización mediante OIDC. En términos generales, la autenticación funciona de la siguiente manera:

* Cuando un usuario anónimo selecciona el botón de inicio de sesión o solicita una página con el [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado, se le redirige a la página de inicio de sesión de la aplicación (`/authentication/login`).
* En la página de inicio de sesión, la biblioteca de autenticación se prepara para un redireccionamiento al punto de conexión de autorización. El punto de conexión de autorización está fuera de la aplicación de Blazor WebAssembly y se puede hospedar en un origen independiente. El punto de conexión es responsable de determinar si el usuario está autenticado y de emitir uno o más tokens en respuesta. La biblioteca de autenticación proporciona una devolución de llamada de inicio de sesión para recibir la respuesta de autenticación.
  * Si el usuario no está autenticado, se le redirige al sistema de autenticación subyacente, que normalmente es ASP.NET Core Identity.
  * Si el usuario ya se ha autenticado, el punto de conexión de autorización genera los tokens adecuados y devuelve al explorador al punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`).
* Cuando la aplicación de Blazor WebAssembly carga el punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`), se procesa la respuesta de autenticación.
  * Si el proceso de autenticación se completa correctamente, el usuario se autentica y, opcionalmente, se devuelve a la dirección URL protegida original que haya solicitado.
  * Si por algún motivo se produce un error en el proceso de autenticación, se envía al usuario a la página de inicio de sesión con errores (`/authentication/login-failed`) y se muestra un error.

## <a name="authentication-component"></a>Componente de `Authentication`

El componente `Authentication` (`Pages/Authentication.razor`) controla las operaciones de autenticación remota y permite a la aplicación:

* Configurar rutas de aplicación para los estados de autenticación.
* Establecer el contenido de la interfaz de usuario para los estados de autenticación.
* Administrar el estado de la autenticación.

Las acciones de autenticación, como el registro o la firma de un usuario, se pasan al componente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> del marco de Blazor, que persiste y controla el estado en las operaciones de autenticación.

Para obtener más información y ejemplos, vea <xref:blazor/security/webassembly/additional-scenarios>.

## <a name="authorization"></a>Autorización

En aplicaciones de Blazor WebAssembly, las comprobaciones de autorización pueden omitirse porque los usuarios pueden modificar todos los códigos del lado cliente. Lo mismo se aplica a todas las tecnologías de aplicaciones del lado cliente, incluidas las plataformas JavaScript SPA o las aplicaciones nativas para cualquier sistema operativo.

**Realice siempre las comprobaciones de autorización en el servidor dentro de cualquier punto de conexión de la API al que acceda su aplicación del lado cliente.**

## <a name="require-authorization-for-the-entire-app"></a>Requerimiento de autorización para toda la aplicación

Aplique el [atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) ([documentación de la API ](xref:System.Web.Mvc.AuthorizeAttribute)) a cada componente Razor de la aplicación siguiendo una de las estrategias siguientes:

* Uso de la directiva [`@attribute`](xref:mvc/views/razor#attribute) en el archivo `_Imports.razor`:

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Agregue el atributo a cada componente Razor de la carpeta `Pages`.

> [!NOTE]
> **No** se admite la configuración de un elemento <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> en una directiva con <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A>.

## <a name="refresh-tokens"></a>Tokens de actualización

Los tokens de actualización no se pueden proteger en el lado cliente en aplicaciones de Blazor WebAssembly. Por lo tanto, no se deben enviar a la aplicación para su uso directo.

Los tokens de actualización se pueden mantener y usar en la aplicación del lado servidor en una solución Blazor WebAssembly hospedada para acceder a las API de terceros. Para obtener más información, vea <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="establish-claims-for-users"></a>Establecimiento de notificaciones para usuarios

Las aplicaciones a menudo requieren notificaciones para usuarios basadas en una llamada de API web a un servidor. Por ejemplo, las notificaciones se usan con frecuencia para [establecer autorizaciones](xref:blazor/security/index#authorization) en una aplicación. En estos casos, la aplicación solicita un token de acceso para acceder al servicio y usa dicho token para obtener los datos de usuario para las notificaciones. En los siguientes temas podrá ver algunos ejemplo:

* [Otros escenarios: Personalizar el usuario](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a>Azure App Service en Linux con Identity Server

Al realizar una implementación en Azure App Service en Linux con Identity Server, especifique el emisor de forma explícita. Para obtener más información, vea <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="windows-authentication"></a>Autenticación de Windows

No se recomienda usar la autenticación de Windows con Blazor Webassembly ni ningún otro marco de SPA. Se recomienda usar protocolos basados en tokens, en lugar de la autenticación de Windows, como OIDC con Servicios de federación de Active Directory (AD FS).

Si se usa la autenticación de Windows con Blazor Webassembly o con cualquier otro marco de SPA, se necesitan medidas adicionales para proteger la aplicación ante tokens de falsificación de solicitud entre sitios (CSRF). Los mismos problemas que se aplican a las cookies se aplican a la autenticación de Windows y, además, la autenticación de Windows no ofrece ningún mecanismo para evitar el uso compartido del contexto de autenticación entre orígenes. Las aplicaciones que utilizan la autenticación de Windows sin la protección adicional de CSRF se deben limitar, al menos, a la intranet de una organización y no usarse en Internet.

Para obtener información, vea <xref:security/anti-request-forgery>.

## <a name="implementation-guidance"></a>Guía de implementación

En los artículos de esta *introducción* se ofrecen detalles sobre la autenticación de usuarios en aplicaciones de Blazor WebAssembly con proveedores específicos.

Aplicaciones de Blazor WebAssembly independientes:

* [Instrucciones generales para los proveedores de OIDC y la biblioteca de autenticación WebAssembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Cuentas de Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Aplicaciones de Blazor WebAssembly hospedadas:

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Servidor de Identity](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

En los siguientes artículos encontrará más instrucciones de configuración:

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Para obtener más instrucciones de configuración, vea <xref:blazor/security/webassembly/additional-scenarios>.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:
  * Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.
  * Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.
