---
title: Autenticación de usuarios con WS-Federation en ASP.NET Core
author: chlowell
description: En este tutorial se muestra cómo usar WS-Federation en una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053376"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Autenticación de usuarios con WS-Federation en ASP.NET Core

En este tutorial se muestra cómo permitir a los usuarios iniciar sesión con un proveedor de autenticación de WS-Federation como Servicios de federación de Active Directory (AD FS) (ADFS) o [Azure Active Directory](/azure/active-directory/) (AAD). Usa la aplicación de ejemplo ASP.NET Core descrita en [Facebook, Google y la autenticación de proveedor externo](xref:security/authentication/social/index).

En el caso de las aplicaciones ASP.NET Core, [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)proporciona compatibilidad con WS-Federation. Este componente se traslada de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) y comparte muchos de los mecanismos de ese componente. Sin embargo, los componentes se diferencian en un par de formas importantes.

De forma predeterminada, el nuevo middleware:

* No permite inicios de sesión no solicitados. Esta característica del protocolo WS-Federation es vulnerable a ataques de XSRF. Sin embargo, se puede habilitar con la `AllowUnsolicitedLogins` opción.
* No comprueba los mensajes de inicio de sesión de todos los formularios. Solo `CallbackPath` se comprueban los inicios de sesión de las solicitudes a. el `CallbackPath` valor predeterminado es, `/signin-wsfed` pero se puede cambiar a través de la propiedad [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) heredada de la clase [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Esta ruta de acceso se puede compartir con otros proveedores de autenticación habilitando la opción [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Registrar la aplicación con Active Directory

### <a name="active-directory-federation-services"></a>Servicios de federación de Active Directory

* Abra el **Asistente para agregar relación de confianza para usuario autenticado** desde la consola de administración de ADFS:

![Asistente para agregar relación de confianza para usuario autenticado: Página principal](ws-federation/_static/AdfsAddTrust.png)

* Elija escribir los datos manualmente:

![Asistente para agregar relación de confianza para usuario autenticado: seleccionar origen de datos](ws-federation/_static/AdfsSelectDataSource.png)

* Escriba un nombre para mostrar para el usuario de confianza. El nombre no es importante para la aplicación ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) carece de compatibilidad con el cifrado de tokens, por lo que no configure un certificado de cifrado de tokens:

![Asistente para agregar relación de confianza para usuario autenticado: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* Habilite la compatibilidad con WS-Federation protocolo pasivo, mediante la dirección URL de la aplicación. Compruebe que el puerto sea correcto para la aplicación:

![Asistente para agregar relación de confianza para usuario autenticado: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Debe ser una dirección URL HTTPS. IIS Express puede proporcionar un certificado autofirmado al hospedar la aplicación durante el desarrollo. Kestrel requiere la configuración manual del certificado. Consulte la [documentación de Kestrel](xref:fundamentals/servers/kestrel) para obtener más información.

* Haga clic en **siguiente** a través del resto del asistente y **cierre** al final.

* ASP.NET Core Identity requiere una notificación de **identificador de nombre** . Agregue uno en el cuadro de diálogo **editar reglas de notificaciones** :

![Editar reglas de notificación](ws-federation/_static/EditClaimRules.png)

* En el **Asistente para agregar regla de notificación de transformación** , deje seleccionada la plantilla predeterminada **Enviar atributos LDAP como notificaciones** y haga clic en **siguiente** . Agregue una regla que asigne el atributo LDAP **Sam-Account-Name** a la notificaciones salientes de **ID. de nombre** :

![Asistente para agregar regla de notificaciones de transformación: configurar regla de notificaciones](ws-federation/_static/AddTransformClaimRule.png)

* Haga clic en **Finalizar**  >  **Aceptar** en la ventana **editar reglas de notificaciones** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Vaya a la hoja registros de aplicaciones del inquilino de AAD. Haga clic en **nuevo registro de aplicaciones** :

![Azure Active Directory: Registros de aplicaciones](ws-federation/_static/AadNewAppRegistration.png)

* Escriba un nombre para el registro de la aplicación. Esto no es importante para la aplicación ASP.NET Core.
* Escriba la dirección URL en la que escucha la aplicación como la **dirección URL de inicio de sesión** :

![Azure Active Directory: creación del registro de aplicaciones](ws-federation/_static/AadCreateAppRegistration.png)

* Haga clic en **extremos** y anote la dirección URL del **documento de metadatos de Federación** . Este es el WS-Federation middleware `MetadataAddress` :

![Azure Active Directory: puntos de conexión](ws-federation/_static/AadFederationMetadataDocument.png)

* Navegue al nuevo registro de aplicaciones. Haga clic en **exponer una API** . Haga clic en ID. de aplicación URI **establecer**  >  **Guardar** . Anote el URI del  **identificador** de la aplicación. Este es el WS-Federation middleware `Wtrealm` :

![Azure Active Directory: propiedades de registro de aplicaciones](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a>Usar WS-Federation sin ASP.NET Core Identity

El middleware de WS-Federation se puede usar sin Identity . Por ejemplo:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a>Agregue WS-Federation como proveedor de inicio de sesión externo para ASP.NET Core Identity

* Agregue una dependencia de [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) al proyecto.
* Agregue WS-Federation a `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Iniciar sesión con WS-Federation

Vaya a la aplicación y haga clic en el vínculo **iniciar sesión** en el encabezado de navegación. Hay una opción para iniciar sesión con WsFederation: ![ Página de inicio de sesión](ws-federation/_static/WsFederationButton.png)

Con ADFS como proveedor, el botón redirige a una página de inicio de sesión de ADFS: ![ Página de inicio de sesión de ADFS](ws-federation/_static/AdfsLoginPage.png)

Con Azure Active Directory como proveedor, el botón redirige a una página de inicio de sesión de AAD: ![ Página de inicio de sesión de AAD.](ws-federation/_static/AadSignIn.png)

Un inicio de sesión correcto para un nuevo usuario redirige a la página de registro del usuario de la aplicación: ![ registro (página)](ws-federation/_static/Register.png)
