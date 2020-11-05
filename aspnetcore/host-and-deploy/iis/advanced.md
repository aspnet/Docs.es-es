---
title: Configuración avanzada
author: rick-anderson
description: Configuración avanzada con el módulo de ASP.NET Core e Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058621"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Configuración avanzada del módulo de ASP.NET Core e IIS

En este artículo se describen las opciones de configuración y los escenarios avanzados para el módulo de ASP.NET Core e IIS.

## <a name="modify-the-stack-size"></a>Modificación del tamaño de la pila

*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*

Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en el archivo `web.config`. El tamaño predeterminado es de 1 048 576 bytes (1 MB). En el ejemplo siguiente, se cambia el tamaño de la pila a 2 MB (2 097 152 bytes):

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>La configuración de proxy usa el protocolo HTTP y un token de emparejamiento

*Solo se aplica al hospedaje fuera de proceso.*

El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP. No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.

Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente. El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`). El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy. El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno. Si los valores del token no coinciden, la solicitud se registra y se rechaza. No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor. Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>El módulo ASP.NET Core con una configuración compartida de IIS

El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta `TrustedInstaller`. Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo `applicationHost.config` del recurso compartido.

Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:

1. Deshabilite la configuración compartida de IIS.
1. Ejecute el instalador.
1. Exporte el archivo `applicationHost.config` actualizado en el recurso compartido de archivos.
1. Vuelva a habilitar la configuración compartida de IIS.

## <a name="data-protection"></a>Protección de datos

La [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) la usan varios [middlewares](xref:fundamentals/middleware/index) de ASP.NET Core, incluidos los que se emplean en la autenticación. Aunque el código de usuario no llame a las API de protección de datos, la protección de datos se debe configurar con un script de implementación o en el código de usuario para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente. Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.

Si el conjunto de claves de protección de datos se almacena en memoria cuando se reinicia la aplicación:

* Todos los tokens de autenticación basados en cookie se invalidan. 
* Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud. 
* Ya no se pueden descifrar los datos protegidos con el conjunto de claves. Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.

Para configurar la protección de datos en IIS para conservar el conjunto de claves, use **uno** de los enfoques siguientes:

* **Creación de claves del Registro de protección de datos**

  Las claves de protección de datos que las aplicaciones de ASP.NET Core usan se almacenan en el registro externo a las aplicaciones. Para conservar las claves de una determinada aplicación, cree claves del Registro para el grupo de aplicaciones.

  En las instalaciones independientes de IIS que no son de granja de servidores web, puede usar el [script de PowerShell Provision-AutoGenKeys.ps1 de protección de datos](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada grupo de aplicaciones usado con una aplicación de ASP.NET Core. Este script crea una clave del Registro en el registro HKLM que solo es accesible para la cuenta de proceso de trabajo del grupo de aplicaciones de la aplicación. Las claves se cifran en reposo mediante DPAPI con una clave de equipo.

  En escenarios de granja de servidores web, una aplicación puede configurarse para usar una ruta de acceso UNC con el fin de almacenar su conjunto de claves de protección de datos. De forma predeterminada, las claves no se cifran. Asegúrese de que los permisos de archivo de un recurso compartido de red se limitan a la cuenta de Windows en la que se ejecuta la aplicación. Puede usar un certificado X509 para proteger las claves en reposo. Considere un mecanismo que permita a los usuarios cargar certificados. coloque los certificados en el almacén de certificados de confianza del usuario y asegúrese de que están disponibles en todos los equipos en los que se ejecuta la aplicación del usuario. Para obtener más información, vea <xref:security/data-protection/configuration/overview>.

* **Configurar el grupo de aplicaciones de IIS para cargar el perfil de usuario**

  Esta opción está en la sección **Modelo de proceso** , en la **Configuración avanzada** del grupo de aplicaciones. Establezca **Cargar perfil de usuario** en `True`. Cuando se establece en `True`, las claves se almacenan en el directorio del perfil de usuario y se protegen mediante DPAPI con una clave específica de la cuenta de usuario. Las claves se conservan en la carpeta `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys`.

  También se debe habilitar el [atributo `setProfileEnvironment`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones. El valor predeterminado de `setProfileEnvironment` es `true`. En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`. Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:

  1. Vaya a la carpeta `%windir%/system32/inetsrv/config`.
  1. Abra el archivo `applicationHost.config` .
  1. Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.

* **Usar el sistema de archivos como un almacén de conjunto de claves**

  Ajuste el código de la aplicación para [usar el sistema de archivos como un almacén de conjunto de claves](xref:security/data-protection/configuration/overview). Use un certificado X509 para proteger el conjunto de claves y asegúrese de que sea un certificado de confianza. Si es un certificado autofirmado, colóquelo en el almacén raíz de confianza.

  Cuando se usa IIS en una granja de servidores web:

  * Use un recurso compartido de archivos al que puedan acceder todos los equipos.
  * Implemente un certificado X509 en cada equipo. Configure la [protección de datos en el código](xref:security/data-protection/configuration/overview).

* **Establecimiento de una directiva a nivel de equipo para la protección de datos**

  El sistema de protección de datos tiene compatibilidad limitada con el establecimiento de una [directiva a nivel de equipo](xref:security/data-protection/configuration/machine-wide-policy) predeterminada para todas las aplicaciones que usan las API de protección de datos. Para obtener más información, vea <xref:security/data-protection/introduction>.

## <a name="iis-configuration"></a>Configuración de IIS

**Sistemas operativos de servidor Windows**

Habilite el rol de servidor **Servidor web (IIS)** y establezca los servicios de rol.

1. Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**. En el paso **Roles de servidor** , active la casilla de **Servidor web (IIS)** .

   ![El rol Servidor web (IIS) se activa en el paso Seleccionar roles de servidor.](index/_static/server-roles-ws2016.png)

1. Después del paso **Características** , el paso **Servicios de rol** se carga para el servidor Web (IIS). Seleccione los servicios de rol IIS que quiera o acepte los servicios de rol predeterminados proporcionados.

   ![Los servicios de rol predeterminados se activan en el paso Seleccionar servicios de rol.](index/_static/role-services-ws2016.png)

   **Autenticación de Windows (opcional)**  
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servidor web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   WebSockets es compatible con ASP.NET Core 1.1 o posterior. Para habilitar WebSockets, expanda los nodos siguientes: **Servidor web** > **Desarrollo de aplicaciones**. Seleccione la característica **Protocolo WebSocket**. Para más información, vea [WebSockets](xref:fundamentals/websockets).

1. Continúe con el paso **Confirmación** para instalar el rol y los servicios de servidor web. No es necesario reiniciar el servidor ni IIS después de instalar el rol **Servidor web (IIS)** .

**Sistemas operativos de escritorio Windows**

Habilite **Consola de administración de IIS** y **Servicios World Wide Web**.

1. Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).

1. Abra el nodo **Internet Information Services**. Abra el nodo **Herramientas de administración web**.

1. Active la casilla de **Consola de administración de IIS**.

1. Active la casilla de **Servicios World Wide Web**.

1. Acepte las características predeterminadas de **Servicios World Wide Web** o personalice las características de IIS.

   **Autenticación de Windows (opcional)**  
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servicios World Wide Web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   WebSockets es compatible con ASP.NET Core 1.1 o posterior. Para habilitar WebSockets, expanda los nodos siguientes: **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**. Seleccione la característica **Protocolo WebSocket**. Para más información, vea [WebSockets](xref:fundamentals/websockets).

1. Si la instalación de IIS requiere un reinicio, reinicie el sistema.

![Consola de administración de IIS y Servicios World Wide Web se activan en Características de Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>Directorios virtuales

Los [directorios virtuales de IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) no son compatibles con aplicaciones ASP.NET Core. Una aplicación puede hospedarse como [subaplicación](#sub-applications).

## <a name="sub-applications"></a>Subaplicaciones

Se puede hospedar una aplicación ASP.NET Core como una [subaplicación IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). La ruta de acceso de la subaplicación se convierte en parte de la dirección URL de la aplicación raíz.

Los vínculos a los recursos estáticos dentro de la aplicación secundaria deben utilizar una notación de una tilde con una barra diagonal (`~/`). La notación de tilde barra diagonal desencadena un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) para anteponer la ruta de acceso de la aplicación secundaria al vínculo relativo representado. Para una aplicación secundaria en `/subapp_path`, una imagen vinculada con `src="~/image.png"` se representa como `src="/subapp_path/image.png"`. El middleware de archivos estáticos de la aplicación raíz no procesa la solicitud de archivo estático. La solicitud se procesa mediante el middleware de archivos estáticos de la aplicación secundaria.

Si el atributo `src` de un recurso estático se establece en una ruta de acceso absoluta (por ejemplo, `src="/image.png"`), el vínculo se representa sin la base de la ruta de acceso de la aplicación secundaria. El middleware de archivos estáticos de la aplicación raíz intenta atender al recurso desde el [web root](xref:fundamentals/index#web-root), lo que resulta en una respuesta *404 - No encontrado* a menos que el recurso estático esté disponible desde la aplicación raíz.

Para hospedar una aplicación ASP.NET Core como aplicación secundaria en otra aplicación ASP.NET Core:

1. Establezca un grupo de aplicaciones para la aplicación secundaria. Establezca **Versión de .NET CLR**  en **Sin código administrado**  porque Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo, no el CLR de escritorio (.NET CLR).

1. Agregue el sitio raíz en el Administrador de IIS con la aplicación secundaria en una carpeta en el sitio raíz.

1. Haga clic con el botón derecho en la carpeta de la aplicación secundaria en el Administrador de IIS y seleccione **Convertir en aplicación**.

1. En el cuadro de diálogo **Agregar aplicación** , use el botón **Seleccionar** en **Grupo de aplicaciones** para asignar el grupo de aplicaciones que ha creado para la aplicación secundaria. Seleccione **Aceptar**.

La asignación de un grupo de aplicaciones independiente de la aplicación secundaria es un requisito cuando se utiliza el modelo de hospedaje en proceso.

Para más información sobre el modelo de hospedaje en proceso y cómo configurar el módulo de ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="application-pools"></a>Grupos de aplicaciones

El aislamiento de los grupos de aplicaciones se determinan mediante el modelo de hospedaje:

* Hospedaje dentro de proceso: es necesario que las aplicaciones se ejecuten en grupos de aplicaciones distintos.
* Hospedaje fuera de proceso: nuestra recomendación es aislar las aplicaciones entre sí ejecutándolas en su propio grupo de aplicaciones.

El valor predeterminado del cuadro de diálogo **Agregar sitio web** de IIS es un único grupo de aplicaciones por aplicación. Cuando se proporciona el **Nombre del sitio** , el texto se transfiere automáticamente al cuadro de texto **Grupo de aplicaciones**. Al agregar el sitio se crea un grupo de aplicaciones con el nombre del sitio.

## <a name="application-pool-no-locidentity"></a>Identity del grupo de aplicaciones

Una cuenta de identidad del grupo de aplicaciones permite ejecutar una aplicación en una cuenta única sin tener que crear ni administrar dominios o cuentas locales. En IIS 8.0 o versiones posteriores, el proceso de trabajo de administración de IIS (WAS) crea una cuenta virtual con el nombre del nuevo grupo de aplicaciones y ejecuta los procesos de trabajo del grupo de aplicaciones en esta cuenta de forma predeterminada. En la Consola de administración de IIS, en la opción **Configuración avanzada** del grupo de aplicaciones, asegúrese de que **Identity** esté establecido para usar `ApplicationPoolIdentity`:

![Cuadro de diálogo Configuración avanzada del grupo de aplicaciones](index/_static/apppool-identity.png)

El proceso de administración de IIS crea un identificador seguro con el nombre del grupo de aplicaciones en el sistema de seguridad de Windows. Los recursos se pueden proteger mediante esta identidad. Sin embargo, no es una cuenta de usuario real ni se muestra en la consola de administración de usuario de Windows.

Si el proceso de trabajo de IIS requiere acceso con privilegios elevados a la aplicación, modifique la lista de control de acceso (ACL) del directorio que contiene la aplicación:

1. Abra el Explorador de Windows y vaya al directorio.

1. Haga clic con el botón derecho en el directorio y seleccione **Propiedades**.

1. En la pestaña **Seguridad** , haga clic en el botón **Editar** y en el botón **Agregar**.

1. Haga clic en el botón **Ubicaciones** y asegúrese de seleccionar el sistema.

1. Escriba el formato `IIS AppPool\{APP POOL NAME}`, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones, en la sección **Escribir los nombres de objeto para seleccionar**. Haga clic en el botón **Comprobar nombres**. Para *DefaultAppPool* compruebe los nombres con `IIS AppPool\DefaultAppPool`. Cuando el botón **Comprobar nombres** está seleccionado, un valor de `DefaultAppPool` se indica en el área de los nombres de objeto. No es posible escribir el nombre del grupo de aplicaciones directamente en el área de los nombres de objeto. Use el formato `IIS AppPool\{APP POOL NAME}`, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones, al comprobar el nombre del objeto.

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: el nombre del grupo de aplicaciones de "DefaultAppPool" se anexa al "IIS AppPool\" en el área de los nombres de objeto antes de seleccionar "Comprobar nombres".](index/_static/select-users-or-groups-1.png)

1. Seleccione **Aceptar**.

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: después de seleccionar "Comprobar nombres", el nombre del objeto "DefaultAppPool" se muestra en el área de los nombres de objeto.](index/_static/select-users-or-groups-2.png)

1. Los permisos de lectura y ejecución se deben conceder de forma predeterminada. Proporcione permisos adicionales según sea necesario.

El acceso también se puede conceder mediante un símbolo del sistema con la herramienta **ICACLS**. En el siguiente comando se usa *DefaultAppPool* como ejemplo:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Para más información, consulte el tema [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Compatibilidad con HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación de IIS siguientes:

* En proceso
  * Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
  * Conexión con TLS 1.2 o una versión posterior
* Fuera de proceso
  * Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
  * Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso al [servidor de Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.
  * Conexión con TLS 1.2 o una versión posterior

Para una implementación dentro del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`. Para una implementación fuera del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/1.1`.

Para obtener más información sobre los modelos de hospedaje en proceso y fuera de proceso, consulte <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 está habilitado de forma predeterminada. Las conexiones vuelven a HTTP/1.1 si no se establece una conexión HTTP/2. Para más información sobre la configuración HTTP/2 con implementaciones de IIS, vea [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Solicitudes CORS de preflight

*Esta sección solo se aplica a las aplicaciones de ASP.NET Core que tienen como destino .NET Framework.*

Para una aplicación de ASP.NET Core que tiene como destino .NET Framework, las solicitudes OPTIONS no se pasan a la aplicación de forma predeterminada en IIS. Para obtener información sobre cómo configurar los controladores de IIS de la aplicación en `web.config` para pasar las solicitudes OPTIONS, consulte [Habilitar solicitudes entre orígenes en ASP.NET Web API 2: Cómo funciona la CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Módulo de inicialización de aplicaciones y tiempo de espera de inactividad

Cuando se hospeda en IIS mediante la versión 2 del módulo de ASP.NET Core:

* [Módulo de inicialización de aplicaciones](#application-initialization-module): las aplicaciones hospedadas [dentro de proceso](xref:host-and-deploy/iis/in-process-hosting) o [fuera de proceso](xref:host-and-deploy/iis/out-of-process-hosting) se pueden configurar para iniciarse de forma automática en un reinicio de proceso de trabajo o un reinicio de servidor.
* [Tiempo de espera de inactividad](#idle-timeout): Las aplicaciones hospedadas [dentro de proceso](xref:host-and-deploy/iis/in-process-hosting) se pueden configurar para que no tengan tiempo de espera durante períodos de inactividad.

### <a name="application-initialization-module"></a>Módulo de inicialización de aplicaciones

*Se aplica a las aplicaciones hospedadas dentro de proceso y fuera de proceso.*

[Inicialización de aplicaciones de IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) es una característica de IIS que envía una solicitud HTTP a la aplicación al iniciarse o reciclarse el grupo de aplicaciones. La solicitud desencadena el inicio de la aplicación. De forma predeterminada, IIS emite una solicitud a la dirección URL raíz de la aplicación (`/`) para inicializar esta (consulte los [recursos adicionales](#application-initialization-module-and-idle-timeout-additional-resources) para más detalles sobre la configuración).

Confirme que la característica de rol Inicialización de aplicaciones de IIS está habilitada:

En Windows 7 o sistemas de escritorio posteriores cuando se usa IIS localmente:

1. Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).
1. Abra **Internet Information Services** > **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.
1. Active la casilla de **Inicialización de aplicaciones**.

En Windows Server 2008 R2 o posterior:

1. Abra el **Asistente para agregar roles y características**.
1. En el panel **Seleccionar servicios de rol** , abra el nodo **Desarrollo de aplicaciones**.
1. Active la casilla de **Inicialización de aplicaciones**.

Use cualquiera de los enfoques siguientes para habilitar el módulo de inicialización de aplicaciones para el sitio:

* Mediante el Administrador de IIS:

  1. Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.
  1. El valor predeterminado de **Modo de inicio** es `OnDemand`. Establezca **Modo de inicio** en `AlwaysRunning`. Seleccione **Aceptar**.
  1. Abra el nodo **Sitios** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en la aplicación y seleccione **Administrar sitio web** > **Configuración avanzada**.
  1. El valor predeterminado de **Carga previa activada** es `False`. Establezca **Carga previa activada** en `True`. Seleccione **Aceptar**.

* Mediante `web.config`, agregue el elemento `<applicationInitialization>` con `doAppInitAfterRestart` establecido en `true` a los elementos `<system.webServer>` del archivo `web.config` de la aplicación:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Tiempo de espera de inactividad

*Solo se aplica a las aplicaciones hospedadas dentro de proceso.*

Para evitar la inactividad en la aplicación, establezca el tiempo de espera de inactividad del grupo de aplicaciones mediante el Administrador de IIS:

1. Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.
1. Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.
1. El valor predeterminado de **Tiempo de inactividad (minutos)**  es `20` minutos. Establezca **Tiempo de inactividad (minutos)** en `0` (cero). Seleccione **Aceptar**.
1. Desactive y vuelva a activar el proceso de trabajo.

Para evitar que las aplicaciones hospedadas [fuera de proceso](xref:host-and-deploy/iis/out-of-process-hosting) agoten el tiempo de espera, use cualquiera de los enfoques siguientes:

* Haga ping a la aplicación desde un servicio externo con el fin de mantenerla funcionando.
* Si la aplicación solo hospeda servicios en segundo plano, evite el hospedaje de IIS y use un [servicio de Windows para hospedar la aplicación de ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Recursos adicionales del módulo de inicialización de aplicaciones y del tiempo de espera de inactividad

* [Inicialización de aplicaciones IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicialización de aplicaciones `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).
* [Configuración del modelo de proceso para un grupo de aplicaciones `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Ubicaciones del módulo, el esquema y el archivo de configuración

### <a name="module"></a>Module

**IIS (x86/amd64)** :

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)** :

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Schema

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuración

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* CLI de *iisexpress.exe* : `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Los archivos se pueden encontrar mediante la búsqueda de `aspnetcore` en el archivo `applicationHost.config`.
