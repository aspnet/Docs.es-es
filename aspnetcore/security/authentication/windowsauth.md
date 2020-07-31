---
title: Configurar la autenticación de Windows en ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo configurar la autenticación de Windows en ASP.NET Core para IIS y HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330694"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurar la autenticación de Windows en ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

La autenticación de Windows (también conocida como autenticación de negociación, Kerberos o NTLM) puede configurarse para ASP.NET Core aplicaciones hospedadas con [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)o [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La autenticación de Windows (también conocida como autenticación de negociación, Kerberos o NTLM) puede configurarse para ASP.NET Core aplicaciones hospedadas con [IIS](xref:host-and-deploy/iis/index) o [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

La autenticación de Windows se basa en el sistema operativo para autenticar a los usuarios de ASP.NET Core aplicaciones. Puede usar la autenticación de Windows cuando el servidor se ejecuta en una red corporativa con Active Directory identidades de dominio o cuentas de Windows para identificar a los usuarios. La autenticación de Windows es más adecuada para entornos de intranet en los que los usuarios, las aplicaciones cliente y los servidores Web pertenecen al mismo dominio de Windows.

> [!NOTE]
> La autenticación de Windows no es compatible con HTTP/2. Los desafíos de autenticación se pueden enviar en respuestas HTTP/2, pero el cliente debe degradar a HTTP/1.1 antes de la autenticación.

## <a name="proxy-and-load-balancer-scenarios"></a>Escenarios de proxy y equilibrador de carga

La autenticación de Windows es un escenario con estado que se usa principalmente en una intranet, donde un proxy o un equilibrador de carga normalmente controla el tráfico entre clientes y servidores. Si se usa un proxy o un equilibrador de carga, la autenticación de Windows solo funciona si el proxy o el equilibrador de carga:

* Controla la autenticación.
* Pasa la información de autenticación del usuario a la aplicación (por ejemplo, en un encabezado de solicitud), que actúa en la información de autenticación.

Una alternativa a la autenticación de Windows en entornos en los que se usan servidores proxy y equilibradores de carga es Active Directory Federated Services (ADFS) con OpenID Connect (OIDC).

## <a name="iisiis-express"></a>IIS/IIS Express

Agregar servicios de autenticación mediante la invocación <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> de (espacio de nombres) en `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Configuración de inicio (depurador)

La configuración de la configuración de inicio solo afecta a las *propiedades/launchSettings.jsdel* archivo para IIS Express y no configura IIS para la autenticación de Windows. La configuración del servidor se explica en la sección [IIS](#iis) .

La plantilla de **aplicación web** disponible a través de Visual Studio o el CLI de .net Core puede configurarse para admitir la autenticación de Windows, que actualiza las *propiedades o el launchSettings.jsen* el archivo automáticamente.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Nuevo proyecto**

1. Cree un nuevo proyecto.
1. Seleccione **Aplicación web de ASP.NET Core**. Seleccione **Siguiente**.
1. Proporcione un nombre en el campo **nombre del proyecto** . Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.
1. Seleccione **cambiar** en **autenticación**.
1. En la ventana **cambiar autenticación** , seleccione **autenticación de Windows**. Seleccione **Aceptar**.
1. Seleccione **Aplicación web**.
1. Seleccione **Crear**.

Ejecute la aplicación. El nombre de usuario aparece en la interfaz de usuario de la aplicación representada.

**Proyecto existente**

Las propiedades del proyecto habilitan la autenticación de Windows y deshabilitan la autenticación anónima:

1. Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Propiedades**.
1. Seleccione la pestaña **Depurar**.
1. Desactive la casilla **Habilitar autenticación anónima**.
1. Active la casilla **Habilitar autenticación de Windows**.
1. Guarde y cierre la página de propiedades.

Como alternativa, las propiedades se pueden configurar en el `iisSettings` nodo del *launchSettings.jsen* el archivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

**Nuevo proyecto**

Ejecute el comando [dotnet New](/dotnet/core/tools/dotnet-new) con el `webapp` argumento (ASP.net Core aplicación web) y el `--auth Windows` modificador:

```dotnetcli
dotnet new webapp --auth Windows
```

**Proyecto existente**

Actualice el `iisSettings` nodo del *launchSettings.jsen* el archivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Al modificar un proyecto existente, confirme que el archivo de proyecto incluye una referencia de paquete para el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **o** el paquete NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

IIS usa el [módulo ASP.net Core](xref:host-and-deploy/aspnet-core-module) para hospedar ASP.net Core aplicaciones. La autenticación de Windows está configurada para IIS a través del archivo de *web.config* . En las secciones siguientes se muestra cómo:

* Proporcione un archivo de *web.config* local que active la autenticación de Windows en el servidor cuando se implemente la aplicación.
* Use el administrador de IIS para configurar el archivo de *web.config* de una aplicación ASP.net Core que ya se ha implementado en el servidor.

Si todavía no lo ha hecho, habilite IIS para hospedar ASP.NET Core aplicaciones. Para obtener más información, vea <xref:host-and-deploy/iis/index>.

Habilite el servicio de función IIS para la autenticación de Windows. Para obtener más información, vea [Habilitar la autenticación de Windows en los servicios de rol de IIS (consulte el paso 2)](xref:host-and-deploy/iis/index#iis-configuration).

El [middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitudes automáticamente de forma predeterminada. Para obtener más información, vea [Host ASP.net Core en Windows con IIS: opciones de IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

El módulo ASP.NET Core está configurado para reenviar el token de autenticación de Windows a la aplicación de forma predeterminada. Para obtener más información, vea [referencia de configuración del módulo ASP.net Core: atributos del elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Use **cualquiera** de los métodos siguientes:

* **Antes de publicar e implementar el proyecto,** agregue el siguiente archivo de *web.config* a la raíz del proyecto:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Cuando el SDK de .NET Core publica el proyecto (sin la `<IsTransformWebConfigDisabled>` propiedad establecida en `true` en el archivo de proyecto), el archivo de *web.config* publicado incluye la `<location><system.webServer><security><authentication>` sección. Para obtener más información sobre la `<IsTransformWebConfigDisabled>` propiedad, vea <xref:host-and-deploy/iis/index#webconfig-file> .

* **Después de publicar e implementar el proyecto,** realice la configuración del servidor con el administrador de IIS:

  1. En el administrador de IIS, seleccione el sitio de IIS en el nodo **sitios** de la barra lateral **conexiones** .
  1. Haga doble clic en **autenticación** en el área **IIS** .
  1. Seleccione **autenticación anónima**. Seleccione **deshabilitar** en la barra lateral de **acciones** .
  1. Seleccione **Autenticación de Windows**. Seleccione **Habilitar** en la barra lateral de **acciones** .

  Cuando se realizan estas acciones, el administrador de IIS modifica el archivo de *web.config* de la aplicación. `<system.webServer><security><authentication>`Se agrega un nodo con la configuración actualizada para `anonymousAuthentication` y `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  La `<system.webServer>` sección agregada al archivo *web.config* por el administrador de IIS está fuera de la sección de la aplicación `<location>` agregada por el SDK de .net Core cuando se publica la aplicación. Dado que la sección se agrega fuera del `<location>` nodo, todas las [aplicaciones secundarias](xref:host-and-deploy/iis/index#sub-applications) de la aplicación actual heredan la configuración. Para evitar la herencia, mueva la `<security>` sección agregada dentro de la `<location><system.webServer>` sección que proporciona el SDK de .net Core.

  Cuando se utiliza el administrador de IIS para agregar la configuración de IIS, solo afecta al archivo de *web.config* de la aplicación en el servidor. Una implementación posterior de la aplicación puede sobrescribir la configuración del servidor si la copia del servidor de *web.config* se reemplaza por el archivo *web.config* del proyecto. Use **cualquiera** de los métodos siguientes para administrar la configuración:

  * Use el administrador de IIS para restablecer la configuración del archivo de *web.config* después de sobrescribir el archivo en la implementación.
  * Agregue un *archivo deweb.config* a la aplicación localmente con la configuración.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

El paquete NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) se puede usar con [Kestrel](xref:fundamentals/servers/kestrel) para admitir la autenticación de Windows mediante Negotiate y Kerberos en Windows, Linux y MacOS.

> [!WARNING]
> Las credenciales se pueden conservar entre las solicitudes de una conexión. *La autenticación Negotiate no debe usarse con servidores proxy a menos que el proxy mantenga una afinidad de conexión 1:1 (una conexión persistente) con Kestrel.*

> [!NOTE]
> El controlador Negotiate detecta si el servidor subyacente admite la autenticación de Windows de forma nativa y si está habilitado. Si el servidor admite la autenticación de Windows pero está deshabilitado, se produce un error que le pide que habilite la implementación del servidor. Cuando la autenticación de Windows está habilitada en el servidor, el controlador de negociación lo reenvía de forma transparente.

Agregue servicios de autenticación invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> y <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> en `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Agregue middleware de autenticación mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> en `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Para obtener más información sobre middleware, vea <xref:fundamentals/middleware/index> .

Se permiten las solicitudes anónimas. Utilice [ASP.net Core autorización](xref:security/authorization/introduction) para desafiar las solicitudes anónimas de autenticación.

### <a name="windows-environment-configuration"></a>Configuración del entorno de Windows

El componente [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) realiza la autenticación de modo de usuario. Los nombres de entidad de seguridad de servicio (SPN) se deben agregar a la cuenta de usuario que ejecuta el servicio, no a la cuenta de equipo. Ejecute `setspn -S HTTP/myservername.mydomain.com myuser` en un shell de comandos administrativo.

### <a name="linux-and-macos-environment-configuration"></a>Configuración del entorno de Linux y macOS

Las instrucciones para unir una máquina Linux o macOS a un dominio de Windows están disponibles en el artículo [conexión Azure Data Studio a la SQL Server mediante la autenticación de Windows: Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Las instrucciones crean una cuenta de equipo para la máquina Linux en el dominio. Los SPN se deben agregar a esa cuenta de equipo.

> [!NOTE]
> Cuando siga las instrucciones que se indican en el artículo [conexión de Azure Data Studio al SQL Server mediante la autenticación de Windows: Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , reemplace `python-software-properties` por `python3-software-properties` si es necesario.

Una vez que la máquina Linux o macOS está unida al dominio, es necesario realizar pasos adicionales para proporcionar un [archivo de fichas de claves](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) con los SPN:

* En el controlador de dominio, agregue nuevos SPN de servicio Web a la cuenta del equipo:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Use [ktpass](/windows-server/administration/windows-commands/ktpass) para generar un archivo de la fichas de claves:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Algunos campos deben especificarse en mayúsculas como se indica.
* Copie el archivo de la fichas de claves en el equipo Linux o macOS.
* Seleccione el archivo de la información de claves a través de una variable de entorno:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Invoque `klist` para mostrar los SPN actualmente disponibles para su uso.

> [!NOTE]
> Un archivo de fichas de claves contiene credenciales de acceso de dominio y debe protegerse en consecuencia.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) admite la autenticación de Windows en modo kernel mediante la autenticación Negotiate, NTLM o básica.

Agregar servicios de autenticación mediante la invocación <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> de (espacio de nombres) en `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Configure el host Web de la aplicación para usar HTTP.sys con la autenticación de Windows (*Program.CS*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>está en el <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espacio de nombres.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys delega en la autenticación de modo kernel con el protocolo de autenticación de Kerberos. La autenticación de modo usuario no se admite con Kerberos y HTTP.sys. Se debe usar la cuenta de equipo para descifrar el token o el vale de Kerberos que se obtiene de Active Directory y que el cliente reenvía al servidor para autenticar al usuario. Registre el nombre de entidad de seguridad de servicio (SPN) para el host, no el usuario de la aplicación.

> [!NOTE]
> HTTP.sys no se admite en la versión 1709 o posterior de nano Server. Para usar la autenticación de Windows y HTTP.sys con nano Server, use un [contenedor Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Para obtener más información sobre Server Core, vea [¿Qué es la opción de instalación Server Core en Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizar a usuarios

El estado de configuración de acceso anónimo determina el modo en que `[Authorize]` `[AllowAnonymous]` se usan los atributos y en la aplicación. En las dos secciones siguientes se explica cómo administrar los Estados de configuración no permitidos y permitidos de acceso anónimo.

### <a name="disallow-anonymous-access"></a>No permitir el acceso anónimo

Cuando se habilita la autenticación de Windows y se deshabilita el acceso anónimo, los `[Authorize]` `[AllowAnonymous]` atributos y no tienen ningún efecto. Si un sitio de IIS está configurado para no permitir el acceso anónimo, la solicitud nunca llega a la aplicación. Por esta razón, el `[AllowAnonymous]` atributo no es aplicable.

### <a name="allow-anonymous-access"></a>Permitir acceso anónimo

Cuando esté habilitada la autenticación de Windows y el acceso anónimo, utilice los `[Authorize]` `[AllowAnonymous]` atributos y. El `[Authorize]` atributo permite proteger los puntos de conexión de la aplicación que requieren autenticación. El `[AllowAnonymous]` atributo invalida el `[Authorize]` atributo en las aplicaciones que permiten el acceso anónimo. Para obtener información sobre el uso de atributos, vea <xref:security/authorization/simple> .

> [!NOTE]
> De forma predeterminada, los usuarios que carecen de autorización para tener acceso a una página presentan una respuesta HTTP 403 vacía. El [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) puede configurarse para proporcionar a los usuarios una mejor experiencia de "acceso denegado".

## <a name="impersonation"></a>Suplantación

ASP.NET Core no implementa la suplantación. Las aplicaciones se ejecutan con la identidad de la aplicación para todas las solicitudes, mediante el grupo de aplicaciones o la identidad del proceso. Si la aplicación debe realizar una acción en nombre de un usuario, use [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) en un [middleware de terminal en línea](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) en `Startup.Configure` . Ejecute una sola acción en este contexto y, a continuación, cierre el contexto.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`no es compatible con las operaciones asincrónicas y no debe usarse en escenarios complejos. Por ejemplo, no se admite el ajuste de solicitudes completas o cadenas de middleware.

::: moniker range=">= aspnetcore-3.0"

Aunque el paquete [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) habilita la autenticación en Windows, Linux y MacOS, la suplantación solo se admite en Windows.

::: moniker-end

## <a name="claims-transformations"></a>Transformaciones de notificaciones

::: moniker range=">= aspnetcore-3.0"

Al hospedar con IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> no se llama a internamente para inicializar un usuario. Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada. Para obtener más información y un ejemplo de código que activa las transformaciones de notificaciones, vea <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Al hospedar con IIS en modo de proceso, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> no se llama a internamente para inicializar un usuario. Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada. Para obtener más información y un ejemplo de código que active las transformaciones de notificaciones al hospedar en proceso, vea <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
