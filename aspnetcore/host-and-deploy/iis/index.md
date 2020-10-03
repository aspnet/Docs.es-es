---
title: Hospedaje de ASP.NET Core en Windows con IIS
author: rick-anderson
description: Obtenga información sobre cómo hospedar aplicaciones de ASP.NET Core en Windows Server Internet Information Services (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: f648837ce42bef4a828d7eda1a6abdfdd8ac07a2
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654041"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hospedaje de ASP.NET Core en Windows con IIS

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

Para obtener una experiencia de tutorial en la publicación de una aplicación de ASP.NET Core en un servidor IIS, vea <xref:tutorials/publish-to-iis>.

[Instalación del conjunto de hospedaje de .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Los siguientes sistemas operativos son compatibles:

* Windows 7 o posterior
* Windows Server 2012 R2 o versiones posteriores

El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener) no funciona en una configuración de proxy inverso con IIS. Use el [servidor Kestrel](xref:fundamentals/servers/kestrel).

Para obtener información sobre el hospedaje en Azure, vea <xref:host-and-deploy/azure-apps/index>.

Para obtener instrucciones de solución de problemas, vea <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plataformas compatibles

Se admiten las aplicaciones publicadas para implementaciones de 32 bits (x86) y 64 bits (x64). Implemente una aplicación de 32 bits con un SDK de .NET Core de 32 bits (x86) a menos que la aplicación:

* Requiera el espacio de direcciones de memoria virtual más grande disponible para una aplicación de 64 bits.
* Requiera el tamaño de la pila IIS más grande.
* Tenga dependencias nativas de 64 bits.

Las aplicaciones publicadas para 32 bits (x86) deben tener habilitados 32 bits para los grupos de aplicaciones de IIS. Para más información, consulte la sección [Creación del sitio de IIS](#create-the-iis-site).

Use un SDK de .NET Core de 64 bits (x64) para publicar una aplicación de 64 bits. Debe haber un tiempo de ejecución de 64 bits en el sistema host.

## <a name="hosting-models"></a>Modelos de hospedaje

### <a name="in-process-hosting-model"></a>Modelo de hospedaje en proceso

Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS. El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina. IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Realiza la inicialización de aplicaciones.
  * Carga [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Llama a `Program.Main`.
* Controla la duración de la solicitud nativa de IIS.

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada en proceso:

![Módulo ASP.NET Core en el escenario de hospedaje dentro de proceso](index/_static/ancm-inprocess.png)

1. Una solicitud llega de Internet al controlador HTTP.sys en modo kernel.
1. El controlador enruta la solicitud nativa a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).
1. El módulo ASP.NET Core recibe la solicitud nativa y la pasa a IIS HTTP Server (`IISHttpServer`). El servidor HTTP de IIS es una implementación de servidor en proceso para IIS que convierte una solicitud nativa en administrada.

Una vez que el servidor HTTP de IIS procesa la solicitud:

1. La solicitud se envía a la canalización de middleware de ASP.NET Core.
1. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.
1. La respuesta de la aplicación se pasa a IIS a través del servidor HTTP de IIS.
1. IIS envía la respuesta al cliente que inició la solicitud.

El hospedaje en proceso es opcional para las aplicaciones existentes. Las plantillas web de ASP.NET Core usan el modelo de hospedaje en proceso.

`CreateDefaultBuilder` agrega una instancia <xref:Microsoft.AspNetCore.Hosting.Server.IServer> mediante una llamada al método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para iniciar [CoreCLR](/dotnet/standard/glossary#coreclr) y hospedar la aplicación dentro del proceso de trabajo de IIS (*w3wp.exe* o *iisexpress.exe*). Las pruebas de rendimiento indican que el hospedaje de una aplicación .NET Core en proceso proporciona un rendimiento de solicitud considerablemente mayor en comparación con el hospedaje de solicitudes de aplicaciones fuera de proceso y de proxy para [Kestrel](xref:fundamentals/servers/kestrel).

Las aplicaciones publicadas como un único archivo ejecutable no se pueden cargar por el modelo de hospedaje en proceso.

### <a name="out-of-process-hosting-model"></a>Modelo de hospedaje fuera de proceso

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo ASP.NET Core se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:

![Módulo ASP.NET Core en el escenario de hospedaje fuera de proceso](index/_static/ancm-outofprocess.png)

1. Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.
1. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).
1. El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

<!-- make this a bullet list -->
El módulo ASP.NET Core especifica el puerto a través de una variable de entorno en el inicio. La extensión de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configura el servidor para que escuche en `http://localhost:{PORT}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no es compatible con el reenvío de HTTPS. Las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la reenvía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la reenvía de nuevo al cliente HTTP que inició la solicitud.

Para obtener instrucciones sobre la configuración del módulo ASP.NET Core, vea <xref:host-and-deploy/aspnet-core-module>.

Para obtener más información sobre el hospedaje, consulte [Hospedaje en ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuración de aplicación

### <a name="enable-the-iisintegration-components"></a>Habilitación de los componentes de integración con IIS

Al compilar un host en `CreateHostBuilder` (*Program.cs*), llame a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> para permitir la integración de IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Para más información sobre `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.

### <a name="iis-options"></a>Opciones de IIS

**Modelo de hospedaje en proceso**

Para configurar las opciones del servidor de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Con el ejemplo siguiente se deshabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el servidor IIS establece el `HttpContext.User` autenticado mediante la [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el servidor solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para obtener más información, vea [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `AllowSynchronousIO`           | `false` | Si se permiten la E/S sincrónica para `HttpContext.Request` y `HttpContext.Response`. |
| `MaxRequestBodySize`           | `30000000`  | Obtiene o establece el tamaño máximo del cuerpo de solicitud para `HttpRequest`. Tenga en cuenta que el propio IIS tiene el límite de `maxAllowedContentLength`, que se procesará antes que el valor de `MaxRequestBodySize` establecido en `IISServerOptions`. El cambio de `MaxRequestBodySize` no afectará a `maxAllowedContentLength`. Para aumentar `maxAllowedContentLength`, agregue una entrada al archivo *web.config* para establecer `maxAllowedContentLength` en un valor superior. Para más información, consulte [Configuración](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Modelo de hospedaje fuera de proceso**

Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. En el ejemplo siguiente se impide que la aplicación rellene `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el [middleware de integración con IIS](#enable-the-iisintegration-components) establece el `HttpContext.User` autenticado mediante [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el middleware solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para más información, consulte el tema [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `ForwardClientCertificate`     | `true`  | Si `HttpContext.Connection.ClientCertificate` y el encabezado de solicitud `true` está presente, se rellena `MS-ASPNETCORE-CLIENTCERT`. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

El [middleware de integración de IIS](#enable-the-iisintegration-components) y el módulo de ASP.NET Core están configurados para reenviar:

* Esquema (HTTP/HTTPS).
* Dirección IP remota donde se originó la solicitud.

El [middleware de integración de IIS](#enable-the-iisintegration-components) configura el middleware de encabezados reenviados.

Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Archivo web.config

El archivo *web.config* configura el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). La creación, transformación y publicación del archivo *web.config* se controla por medio de un destino de MSBuild (`_TransformWebConfig`) cuando el proyecto se publica. Este destino está incluido entre los destinos del SDK web (`Microsoft.NET.Sdk.Web`). El SDK se establece al inicio del archivo del proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el proyecto no incluye un archivo *web.config*, el archivo se crea con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la [salida publicada](xref:host-and-deploy/directory-structure).

Si el proyecto incluye un archivo *web.config*, el archivo se transforma con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la salida publicada. La transformación no modifica los valores de configuración de IIS del archivo.

El archivo *web.config* puede proporcionar valores de configuración de IIS adicionales que controlan los módulos activos de IIS. Para información sobre los módulos de IIS que son capaces de procesar las solicitudes con aplicaciones ASP.NET Core, vea el tema [Módulos IIS](xref:host-and-deploy/iis/modules).

Para evitar que el SDK web transforme el archivo *web.config*, use la propiedad **\<IsTransformWebConfigDisabled>** en el archivo del proyecto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Al deshabilitar el SDK web para la transformación del archivo, el desarrollador debe establecer el elemento *processPath* y los *argumentos* manualmente. Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Ubicación del archivo web.config

Para configurar el [módulo ASP.NET](xref:host-and-deploy/aspnet-core-module) correctamente, el archivo *web.config* debe estar presente en la ruta [raíz de contenido](xref:fundamentals/index#content-root) (normalmente la ruta de acceso base de la aplicación) de la aplicación implementada. Se trata de la misma ubicación que la ruta de acceso física del sitio web proporcionada a IIS. El archivo *web.config* debe estar en la raíz de la aplicación para habilitar la publicación de varias aplicaciones mediante Web Deploy.

Los archivos confidenciales están en la ruta de acceso física de la aplicación, como *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (comentarios de documentación XML) y *\<assembly>.deps.json*. Si el archivo *web.config* está presente y el sitio se inicia normalmente, IIS no facilita estos archivos confidenciales, en el caso de que se soliciten. Si el archivo *web.config* no está presente, se le asignó un nombre incorrecto o no se puede configurar el sitio para un inicio normal, IIS puede servir archivos confidenciales públicamente.

**El archivo *web.config* debe estar presente en la implementación en todo momento, se le debe asignar un nombre correcto y debe ser capaz de configurar el sitio para el inicio normal. Nunca quite el archivo *web.config* de una implementación de producción.**

### <a name="transform-webconfig"></a>Transformación de web.config

Si necesita transformar *web.config* al realizar la publicación, vea <xref:host-and-deploy/iis/transform-webconfig>. Es posible que necesite transformar *web.config* al realizar la publicación para establecer variables de entorno basadas en la configuración, el perfil o el entorno.

## <a name="iis-configuration"></a>Configuración de IIS

**Sistemas operativos de servidor Windows**

Habilite el rol de servidor **Servidor web (IIS)** y establezca los servicios de rol.

1. Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**. En el paso **Roles de servidor**, active la casilla de **Servidor web (IIS)** .

   ![El rol Servidor web (IIS) se activa en el paso Seleccionar roles de servidor.](index/_static/server-roles-ws2016.png)

1. Después del paso **Características**, el paso **Servicios de rol** se carga para el servidor Web (IIS). Seleccione los servicios de rol IIS que quiera o acepte los servicios de rol predeterminados proporcionados.

   ![Los servicios de rol predeterminados se activan en el paso Seleccionar servicios de rol.](index/_static/role-services-ws2016.png)

   **Autenticación de Windows (opcional)**  
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servidor web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

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
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servicios World Wide Web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   WebSockets es compatible con ASP.NET Core 1.1 o posterior. Para habilitar WebSockets, expanda los nodos siguientes: **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**. Seleccione la característica **Protocolo WebSocket**. Para más información, vea [WebSockets](xref:fundamentals/websockets).

1. Si la instalación de IIS requiere un reinicio, reinicie el sistema.

![Consola de administración de IIS y Servicios World Wide Web se activan en Características de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalación del conjunto de hospedaje de .NET Core

Instale el *conjunto de hospedaje de .NET Core* en el sistema de hospedaje. El lote instala .NET Core Runtime, .NET Core Library y el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). El módulo permite que las aplicaciones ASP.NET Core se ejecuten detrás de IIS.

> [!IMPORTANT]
> Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto. Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.
>
> Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Descarga directa (versión actual)

Descargue al instalador mediante el vínculo siguiente:

[Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Versiones anteriores del instalador

Para obtener una versión anterior del instalador:

1. Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
1. Seleccione la versión de .NET Core que quiera.
1. En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.
1. Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.

> [!WARNING]
> Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft. Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalación del conjunto de hospedaje

1. Ejecute el instalador en el servidor. Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:

   * `OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core. Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET). Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86. Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits. Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (*applicationHost.config*) está en la misma máquina que la instalación de IIS. *Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.* Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:

   ```console
   net stop was /y
   net start w3svc
   ```
   Al reiniciar IIS, se recoge un cambio en la variable PATH del sistema, que es una variable de entorno, realizado por el programa de instalación.

ASP.NET Core no adopta el comportamiento de puesta al día para las versiones de revisión de los paquetes de marco compartidos. Después de actualizar el marco compartido mediante la instalación de un nuevo lote de hospedaje, reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalación de Web Deploy al publicar con Visual Studio

Al implementar aplicaciones en servidores con [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale la versión más reciente de Web Deploy en el servidor. Para instalar Web Deploy, use el [Instalador de plataforma web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) u obtenga un instalador directamente desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). El método preferido es usar WebPI. WebPI ofrece una instalación independiente y una configuración para los proveedores de hospedaje.

## <a name="create-the-iis-site"></a>Creación del sitio de IIS

1. En el sistema de hospedaje, cree una carpeta para que contenga los archivos y las carpetas publicados de la aplicación. En un paso posterior, la ruta de acceso de la carpeta se proporciona a IIS como la ruta de acceso física a la aplicación. Para obtener más información sobre el diseño de carpetas y archivos de implementación de una aplicación, consulte <xref:host-and-deploy/directory-structure>.

1. En Administrador de IIS, abra el nodo del servidor en el panel **Conexiones**. Haga clic con el botón derecho en la carpeta **Sitios**. Haga clic en **Agregar sitio web** en el menú contextual.

1. Proporcione el **Nombre del sitio** y establezca la **Ruta de acceso física** a la carpeta de implementación de la aplicación. Proporcione la configuración de **Enlace** y cree el sitio web seleccionando **Aceptar**:

   ![Proporcione el nombre del sitio, la ruta de acceso física y el nombre de host en el paso Agregar sitio web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Los enlaces de carácter comodín de nivel superior (`http://*:80/` y `http://+:80`) **no** se deben usar. Los enlaces de carácter comodín de nivel superior pueden exponer su aplicación a vulnerabilidades de seguridad. Esto se aplica tanto a los caracteres comodín fuertes como a los débiles. Use nombres de host explícitos en lugar de caracteres comodín. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.mysub.com`) no suponen este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

1. En el nodo del servidor, seleccione **Grupos de aplicaciones**.

1. Haga clic con el botón derecho en el grupo de aplicaciones del sitio y seleccione **Configuración básica** en el menú contextual.

1. En la ventana **Modificar grupo de aplicaciones**, establezca **Versión de .NET CLR** en **Sin código administrado**:

   ![Establezca Sin código administrado para Versión de .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core se ejecuta en un proceso independiente y administra el runtime. ASP.NET Core no se basa en la carga de CLR de escritorio (.NET CLR). Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo. El establecimiento de **Versión de .NET CLR** en **Sin código administrado** es opcional, pero no es lo recomendable.

1. *ASP.NET Core 2.2 o posterior*:

   * En el caso de las [implementaciones independientes](/dotnet/core/deploying/#self-contained-deployments-scd) de 32 bits (x86) publicadas con un SDK de 32 bits que use el [modelo de hospedaje en proceso](#in-process-hosting-model), habilite el grupo de aplicaciones para 32 bits. En el Administrador de IIS, vaya a **Grupos de aplicaciones** de la barra lateral **Conexiones**. Seleccione el grupo de aplicaciones de la aplicación. En la barra lateral **Acciones**, seleccione **Configuración avanzada**. Establezca **Habilitar aplicaciones de 32 bits** en `True`. 

   * en el caso de las [implementaciones independientes](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) en las que se use un [modelo de hospedaje en proceso](#in-process-hosting-model), deshabilite el grupo de aplicaciones de los procesos de 32 bits (x86). En el Administrador de IIS, vaya a **Grupos de aplicaciones** en la barra lateral **Conexiones**. Seleccione el grupo de aplicaciones de la aplicación. En la barra lateral **Acciones**, seleccione **Configuración avanzada**. Establezca **Habilitar aplicaciones de 32 bits** en `False`. 

1. Confirme que la identidad del modelo de proceso tiene los permisos adecuados.

   Si cambia la identidad predeterminada del grupo de aplicaciones (**Modelo de proceso** >  **Identity** ) de **ApplicationPoolIdentity** a otra identidad, compruebe que la nueva identidad tenga los permisos necesarios para acceder a la carpeta de la aplicación, la base de datos y otros recursos necesarios. Por ejemplo, el grupo de aplicaciones requiere acceso de lectura y escritura a las carpetas donde la aplicación lee y escribe archivos.

**Configuración de la autenticación de Windows (opcional)**  
Para más información, consulte [Configurar la autenticación de Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Implementación de la aplicación

Implemente la aplicación en la carpeta **Ruta de acceso física** de IIS que se creó en la sección [Creación del sitio de IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) es el mecanismo recomendado para la implementación, pero existen varias opciones para mover la aplicación desde la carpeta *publicar* del proyecto a la carpeta de implementación del sistema host.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy con Visual Studio

Vea el tema [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Perfiles de publicación de Visual Studio para la implementación de aplicaciones de ASP.NET Core) para obtener más información sobre cómo crear un perfil de publicación para su uso con Web Deploy. Si el proveedor de hospedaje proporciona un perfil de publicación o permite crear uno, descargue su perfil e impórtelo mediante el cuadro de diálogo **Publicar** de Visual Studio:

![Cuadro de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy fuera de Visual Studio

También puede usar [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) fuera de Visual Studio desde la línea de comandos. Para más información, vea [Web Deployment Tool (Herramienta de implementación web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativas a Web Deploy

Use cualquiera de los métodos disponibles para mover la aplicación al sistema de hospedaje, como copia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/).

Para obtener más información sobre ASP.NET Core en IIS, vea la sección [Recursos de implementación para administradores de IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Examinar el sitio web

Cuando la aplicación esté implementada en el sistema de hospedaje, realice una solicitud a uno de los puntos de conexión públicos de la aplicación.

En el ejemplo siguiente, el sitio se enlaza a un **Nombre de host** de IIS de `www.mysite.com` en el **Puerto** `80`. Se realiza una solicitud a `http://www.mysite.com`:

![El explorador Microsoft Edge ha cargado la página de inicio de IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Archivos de implementación bloqueados

Los archivos de la carpeta de implementación se bloquean cuando se ejecuta la aplicación. Los archivos bloqueados no se pueden sobrescribir durante la implementación. Para liberar archivos bloqueados de una implementación, detenga el grupo de aplicaciones mediante **uno** de los enfoques siguientes:

* Use Web Deploy con una referencia a `Microsoft.NET.Sdk.Web` en el archivo de proyecto. Se coloca un archivo *app_offline.htm* en la raíz del directorio de aplicación web. Cuando el archivo está presente, el módulo de ASP.NET Core cierra correctamente la aplicación y proporciona el archivo *app_offline.htm* durante la implementación. Para más información, vea [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm) (Referencia de configuración del módulo de ASP.NET Core).
* Detenga manualmente el grupo de aplicaciones en el Administrador de IIS en el servidor.
* Utilice PowerShell para colocar *app_offline.htm* (es necesario PowerShell 5 o una versión posterior):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Protección de datos

La [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) la usan varios [middlewares](xref:fundamentals/middleware/index) de ASP.NET Core, incluidos los que se emplean en la autenticación. Aunque el código de usuario no llame a las API de protección de datos, la protección de datos se debe configurar con un script de implementación o en el código de usuario para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente. Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.

Si el conjunto de claves se almacena en memoria cuando se reinicia la aplicación:

* Todos los tokens de autenticación basados en cookie se invalidan. 
* Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud. 
* Ya no se pueden descifrar los datos protegidos con el conjunto de claves. Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.

Para configurar la protección de datos en IIS para conservar el conjunto de claves, use **uno** de los enfoques siguientes:

* **Crear claves del Registro de protección de datos**

  Las claves de protección de datos que las aplicaciones de ASP.NET usan se almacenan en el Registro externo a las aplicaciones. Para conservar las claves de una determinada aplicación, cree claves del Registro para el grupo de aplicaciones.

  En las instalaciones independientes de IIS que no son de granja de servidores web, puede usar el [script de PowerShell Provision-AutoGenKeys.ps1 de protección de datos](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada grupo de aplicaciones usado con una aplicación de ASP.NET Core. Este script crea una clave del Registro en el registro HKLM que solo es accesible a la cuenta de proceso de trabajo del grupo de aplicaciones de la aplicación. Las claves se cifran en reposo mediante DPAPI con una clave de equipo.

  En escenarios de granja de servidores web, una aplicación puede configurarse para usar una ruta de acceso UNC para almacenar su conjunto de claves de protección de datos. De forma predeterminada, las claves de protección de datos no se cifran. Asegúrese de que los permisos de archivo de un recurso compartido de red se limitan a la cuenta de Windows bajo la que se ejecuta la aplicación. Puede usar un certificado X509 para proteger las claves en reposo. Considere un mecanismo que permita a los usuarios cargar certificados: coloque los certificados en el almacén de certificados de confianza del usuario y asegúrese de que están disponibles en todos los equipos en los que se ejecuta la aplicación del usuario. Vea [Configurar la protección de datos en ASP.NET Core](xref:security/data-protection/configuration/overview) para más información.

* **Configurar el grupo de aplicaciones de IIS para cargar el perfil de usuario**

  Esta opción está en la sección **Modelo de proceso**, en la **Configuración avanzada** del grupo de aplicaciones. Establezca **Cargar perfil de usuario** en `True`. Cuando se establece en `True`, las claves se almacenan en el directorio del perfil de usuario y se protegen mediante DPAPI con una clave específica de la cuenta de usuario. Las claves se conservan en la carpeta *%HOME%\ASP.NET\DataProtection-Keys*.

  También se debe habilitar el [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones. El valor predeterminado de `setProfileEnvironment` es `true`. En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`. Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:

  1. Vaya a la carpeta *%windir%/system32/inetsrv/config*.
  1. Abra el archivo *applicationHost.config*.
  1. Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.

* **Usar el sistema de archivos como un almacén de conjunto de claves**

  Ajuste el código de la aplicación para [usar el sistema de archivos como un almacén de conjunto de claves](xref:security/data-protection/configuration/overview). Use un certificado X509 para proteger el conjunto de claves y asegúrese de que sea un certificado de confianza. Si es un certificado autofirmado, colóquelo en el almacén raíz de confianza.

  Cuando se usa IIS en una granja de servidores web:

  * Use un recurso compartido de archivos al que puedan acceder todos los equipos.
  * Implemente un certificado X509 en cada equipo. Configure la [protección de datos en el código](xref:security/data-protection/configuration/overview).

* **Establecer una directiva para todo el equipo para la protección de datos**

  El sistema de protección de datos tiene compatibilidad limitada con el establecimiento de una [directiva de equipo](xref:security/data-protection/configuration/machine-wide-policy) predeterminada para todas las aplicaciones que usan las API de protección de datos. Para obtener más información, vea <xref:security/data-protection/introduction>.

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

1. En el cuadro de diálogo **Agregar aplicación**, use el botón **Seleccionar** en **Grupo de aplicaciones** para asignar el grupo de aplicaciones que ha creado para la aplicación secundaria. Seleccione **Aceptar**.

La asignación de un grupo de aplicaciones independiente de la aplicación secundaria es un requisito cuando se utiliza el modelo de hospedaje en proceso.

Para más información sobre el modelo de hospedaje en proceso y cómo configurar el módulo de ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Configuración de IIS con web.config

En escenarios de IIS que son funcionales para aplicaciones ASP.NET Core con el módulo ASP.NET Core, la configuración de IIS está influenciada por la sección `<system.webServer>` de *web.config*. Por ejemplo, la configuración de IIS es funcional para la compresión dinámica. Si IIS está configurado en el nivel de servidor para usar compresión dinámica, el elemento `<urlCompression>` del archivo *web.config* de la aplicación puede deshabilitarlo para una aplicación ASP.NET Core.

Para obtener más información, vea los temas siguientes:

* [Referencia de configuración para \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Para establecer variables de entorno para aplicaciones individuales que se ejecutan en grupos de aplicaciones aislados (compatible con IIS 10.0 o posterior), vea la sección *Comando AppCmd.exe* del tema [Variables de entorno\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) de la documentación de referencia de IIS.

## <a name="configuration-sections-of-webconfig"></a>Secciones de configuración de web.config

Las aplicaciones ASP.NET Core no usan las secciones de configuración de aplicaciones ASP.NET 4.x en *web.config* para la configuración:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Las aplicaciones de ASP.NET Core se configuran mediante otros proveedores de configuración. Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Grupos de aplicaciones

El aislamiento de los grupos de aplicaciones se determinan mediante el modelo de hospedaje:

* Hospedaje dentro de proceso: es necesario que las aplicaciones se ejecuten en grupos de aplicaciones distintos.
* Hospedaje fuera de proceso: nuestra recomendación es aislar las aplicaciones entre sí ejecutándolas en su propio grupo de aplicaciones.

El valor predeterminado del cuadro de diálogo **Agregar sitio web** de IIS es un único grupo de aplicaciones por aplicación. Cuando se proporciona el **Nombre del sitio**, el texto se transfiere automáticamente al cuadro de texto **Grupo de aplicaciones**. Al agregar el sitio se crea un grupo de aplicaciones con el nombre del sitio.

## <a name="application-pool-no-locidentity"></a>Identity del grupo de aplicaciones

Una cuenta de identidad del grupo de aplicaciones permite ejecutar una aplicación en una cuenta única sin tener que crear ni administrar dominios o cuentas locales. En IIS 8.0 o versiones posteriores, el proceso de trabajo de administración de IIS (WAS) crea una cuenta virtual con el nombre del nuevo grupo de aplicaciones y ejecuta los procesos de trabajo del grupo de aplicaciones en esta cuenta de forma predeterminada. En la Consola de administración de IIS, en la opción **Configuración avanzada** del grupo de aplicaciones, asegúrese de que **Identity** esté establecido para usar **ApplicationPoolIdentity** :

![Cuadro de diálogo Configuración avanzada del grupo de aplicaciones](index/_static/apppool-identity.png)

El proceso de administración de IIS crea un identificador seguro con el nombre del grupo de aplicaciones en el sistema de seguridad de Windows. Los recursos se pueden proteger mediante esta identidad. Sin embargo, no es una cuenta de usuario real ni se muestra en la consola de administración de usuario de Windows.

Si el proceso de trabajo de IIS requiere acceso con privilegios elevados a la aplicación, modifique la lista de control de acceso (ACL) del directorio que contiene la aplicación:

1. Abra el Explorador de Windows y vaya al directorio.

1. Haga clic con el botón derecho en el directorio y seleccione **Propiedades**.

1. En la pestaña **Seguridad**, haga clic en el botón **Editar** y en el botón **Agregar**.

1. Haga clic en el botón **Ubicaciones** y asegúrese de seleccionar el sistema.

1. Escriba **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** en el área **Escribir los nombres de objeto para seleccionar**. Haga clic en el botón **Comprobar nombres**. Para *DefaultAppPool* compruebe los nombres con **IIS AppPool\DefaultAppPool**. Cuando el botón **Comprobar nombres** está seleccionado, un valor de **DefaultAppPool** se indica en el área de los nombres de objeto. No es posible escribir el nombre del grupo de aplicaciones directamente en el área de los nombres de objeto. Use el formato **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** cuando compruebe el nombre del objeto.

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: el nombre del grupo de aplicaciones de "DefaultAppPool" se anexa al "IIS AppPool\" en el área de los nombres de objeto antes de seleccionar "Comprobar nombres".](index/_static/select-users-or-groups-1.png)

1. Seleccione **Aceptar**.

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: después de seleccionar "Comprobar nombres", el nombre del objeto "DefaultAppPool" se muestra en el área de los nombres de objeto.](index/_static/select-users-or-groups-2.png)

1. Los permisos de lectura y ejecución se deben conceder de forma predeterminada. Proporcione permisos adicionales según sea necesario.

El acceso también se puede conceder mediante un símbolo del sistema con la herramienta **ICACLS**. En el siguiente comando se usa *DefaultAppPool* como ejemplo:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Para más información, consulte el tema [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="cors-preflight-requests"></a>Solicitudes CORS de preflight

*Esta sección solo se aplica a las aplicaciones de ASP.NET Core que tienen como destino .NET Framework.*

Para una aplicación de ASP.NET Core que tiene como destino .NET Framework, las solicitudes OPTIONS no se pasan a la aplicación de forma predeterminada en IIS. Para obtener información sobre cómo configurar los controladores de IIS de la aplicación en *web.config* para pasar las solicitudes OPTIONS, consulte [Habilitar solicitudes entre orígenes en ASP.NET Web API 2: Cómo funciona la CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Módulo de inicialización de aplicaciones y tiempo de espera de inactividad

Cuando se hospeda en IIS mediante la versión 2 del módulo de ASP.NET Core:

* [Módulo de inicialización de aplicaciones](#application-initialization-module): las aplicaciones hospedadas [dentro de proceso](#in-process-hosting-model) o [fuera de proceso](#out-of-process-hosting-model) se pueden configurar para iniciarse de forma automática en un reinicio de proceso de trabajo o un reinicio de servidor.
* [Tiempo de espera de inactividad](#idle-timeout): las aplicaciones hospedadas [dentro de proceso](#in-process-hosting-model) se pueden configurar para que no tengan tiempo de espera durante períodos de inactividad.

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
1. En el panel **Seleccionar servicios de rol**, abra el nodo **Desarrollo de aplicaciones**.
1. Active la casilla de **Inicialización de aplicaciones**.

Use cualquiera de los enfoques siguientes para habilitar el módulo de inicialización de aplicaciones para el sitio:

* Mediante el Administrador de IIS:

  1. Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.
  1. El valor predeterminado de **Modo de inicio** es **OnDemand**. Establezca **Modo de inicio** en **AlwaysRunning**. Seleccione **Aceptar**.
  1. Abra el nodo **Sitios** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en la aplicación y seleccione **Administrar sitio web** > **Configuración avanzada**.
  1. El valor predeterminado de **Carga previa activada** es **False**. Establezca **Carga previa activada** en **True**. Seleccione **Aceptar**.

* Mediante *web.config*, agregue el elemento `<applicationInitialization>` con `doAppInitAfterRestart` establecido en `true` a los elementos `<system.webServer>` del archivo *web.config* de la aplicación:

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
1. El valor predeterminado de **Tiempo de inactividad (minutos)**  es **20** minutos. Establezca **Tiempo de inactividad (minutos)** en **0** (cero). Seleccione **Aceptar**.
1. Desactive y vuelva a activar el proceso de trabajo.

Para evitar que las aplicaciones hospedadas [fuera de proceso](#out-of-process-hosting-model) agoten el tiempo de espera, use cualquiera de los enfoques siguientes:

* Haga ping a la aplicación desde un servicio externo con el fin de mantenerla funcionando.
* Si la aplicación solo hospeda servicios en segundo plano, evite el hospedaje de IIS y use un [servicio de Windows para hospedar la aplicación de ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Recursos adicionales del módulo de inicialización de aplicaciones y del tiempo de espera de inactividad

* [Inicialización de aplicaciones IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicialización de aplicaciones \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).
* [Configuración del modelo de proceso para un grupo de aplicaciones \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Recursos de implementación para administradores de IIS

* [Documentación de IIS](/iis)
* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:index>
* [Sitio oficial de Microsoft IIS](https://www.iis.net/)
* [Biblioteca de contenido técnico de Windows Server](/windows-server/windows-server)
* [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Para obtener una experiencia de tutorial en la publicación de una aplicación de ASP.NET Core en un servidor IIS, vea <xref:tutorials/publish-to-iis>.

[Instalación del conjunto de hospedaje de .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Los siguientes sistemas operativos son compatibles:

* Windows 7 o posterior
* Windows Server 2008 R2 o posterior

El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener) no funciona en una configuración de proxy inverso con IIS. Use el [servidor Kestrel](xref:fundamentals/servers/kestrel).

Para obtener información sobre el hospedaje en Azure, vea <xref:host-and-deploy/azure-apps/index>.

Para obtener instrucciones de solución de problemas, vea <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plataformas compatibles

Se admiten las aplicaciones publicadas para implementaciones de 32 bits (x86) y 64 bits (x64). Implemente una aplicación de 32 bits con un SDK de .NET Core de 32 bits (x86) a menos que la aplicación:

* Requiera el espacio de direcciones de memoria virtual más grande disponible para una aplicación de 64 bits.
* Requiera el tamaño de la pila IIS más grande.
* Tenga dependencias nativas de 64 bits.

Use un SDK de .NET Core de 64 bits (x64) para publicar una aplicación de 64 bits. Debe haber un tiempo de ejecución de 64 bits en el sistema host.

## <a name="hosting-models"></a>Modelos de hospedaje

### <a name="in-process-hosting-model"></a>Modelo de hospedaje en proceso

Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS. El hospedaje en proceso proporciona un rendimiento mejor que en el hospedaje fuera de proceso porque:

* Las solicitudes no se procesan en proxy a través del adaptador de bucle invertido. Un adaptador de bucle invertido es una interfaz de red que devuelve el tráfico de red saliente al mismo equipo.

IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Realiza la inicialización de aplicaciones.
  * Carga [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Llama a `Program.Main`.
* Controla la duración de la solicitud nativa de IIS.

No se admite el modelo de hospedaje en proceso para aplicaciones de ASP.NET Core que tienen como destino .NET Framework.

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada en proceso:

![Módulo ASP.NET Core en el escenario de hospedaje dentro de proceso](index/_static/ancm-inprocess.png)

Una solicitud llega de Internet al controlador HTTP.sys en modo kernel. El controlador enruta la solicitud nativa a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS). El módulo ASP.NET Core recibe la solicitud nativa y la pasa a IIS HTTP Server (`IISHttpServer`). El servidor HTTP de IIS es una implementación de servidor en proceso para IIS que convierte una solicitud nativa en administrada.

Una vez que IIS HTTP Server procesa la solicitud, la envía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. La respuesta de la aplicación se pasa a IIS a través del servidor HTTP de IIS. IIS envía la respuesta al cliente que inició la solicitud.

El hospedaje en proceso es opcional para las aplicaciones existentes, pero, para las plantillas [dotnet new](/dotnet/core/tools/dotnet-new), este modelo es el predeterminado para todos los escenarios de IIS e IIS Express.

`CreateDefaultBuilder` agrega una instancia <xref:Microsoft.AspNetCore.Hosting.Server.IServer> mediante una llamada al método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para iniciar [CoreCLR](/dotnet/standard/glossary#coreclr) y hospedar la aplicación dentro del proceso de trabajo de IIS (*w3wp.exe* o *iisexpress.exe*). Las pruebas de rendimiento indican que el hospedaje de una aplicación .NET Core en proceso proporciona un rendimiento de solicitud considerablemente mayor en comparación con el hospedaje de solicitudes de aplicaciones fuera de proceso y de proxy para el servidor [Kestrel](xref:fundamentals/servers/kestrel).

### <a name="out-of-process-hosting-model"></a>Modelo de hospedaje fuera de proceso

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo ASP.NET Core se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:

![Módulo ASP.NET Core en el escenario de hospedaje fuera de proceso](index/_static/ancm-outofprocess.png)

Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS). El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

El módulo especifica el puerto a través de la variable de entorno en el inicio, y la extensión <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configura el servidor para que escuche en `http://localhost:{PORT}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.

Para obtener instrucciones sobre la configuración del módulo ASP.NET Core, vea <xref:host-and-deploy/aspnet-core-module>.

Para obtener más información sobre el hospedaje, consulte [Hospedaje en ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuración de aplicación

### <a name="enable-the-iisintegration-components"></a>Habilitación de los componentes de integración con IIS

Al compilar un host en `CreateWebHostBuilder` (*Program.cs*), llame a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para permitir la integración de IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Para más información sobre `CreateDefaultBuilder`, consulte <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Opciones de IIS

**Modelo de hospedaje en proceso**

Para configurar las opciones del servidor de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Con el ejemplo siguiente se deshabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el servidor IIS establece el `HttpContext.User` autenticado mediante la [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el servidor solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para obtener más información, vea [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |

**Modelo de hospedaje fuera de proceso**

Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. En el ejemplo siguiente se impide que la aplicación rellene `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el [middleware de integración con IIS](#enable-the-iisintegration-components) establece el `HttpContext.User` autenticado mediante [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el middleware solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para más información, consulte el tema [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `ForwardClientCertificate`     | `true`  | Si `HttpContext.Connection.ClientCertificate` y el encabezado de solicitud `true` está presente, se rellena `MS-ASPNETCORE-CLIENTCERT`. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

El [middleware de integración con IIS](#enable-the-iisintegration-components), que configura el software intermedio de encabezados reenviados, y el módulo de ASP.NET Core están configurados para reenviar el esquema (HTTP/HTTPS) y la dirección IP remota donde se originó la solicitud. Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Archivo web.config

El archivo *web.config* configura el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). La creación, transformación y publicación del archivo *web.config* se controla por medio de un destino de MSBuild (`_TransformWebConfig`) cuando el proyecto se publica. Este destino está incluido entre los destinos del SDK web (`Microsoft.NET.Sdk.Web`). El SDK se establece al inicio del archivo del proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el proyecto no incluye un archivo *web.config*, el archivo se crea con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la [salida publicada](xref:host-and-deploy/directory-structure).

Si el proyecto incluye un archivo *web.config*, el archivo se transforma con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la salida publicada. La transformación no modifica los valores de configuración de IIS del archivo.

El archivo *web.config* puede proporcionar valores de configuración de IIS adicionales que controlan los módulos activos de IIS. Para información sobre los módulos de IIS que son capaces de procesar las solicitudes con aplicaciones ASP.NET Core, vea el tema [Módulos IIS](xref:host-and-deploy/iis/modules).

Para evitar que el SDK web transforme el archivo *web.config*, use la propiedad **\<IsTransformWebConfigDisabled>** en el archivo del proyecto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Al deshabilitar el SDK web para la transformación del archivo, el desarrollador debe establecer el elemento *processPath* y los *argumentos* manualmente. Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Ubicación del archivo web.config

Para configurar el [módulo ASP.NET](xref:host-and-deploy/aspnet-core-module) correctamente, el archivo *web.config* debe estar presente en la ruta [raíz de contenido](xref:fundamentals/index#content-root) (normalmente la ruta de acceso base de la aplicación) de la aplicación implementada. Se trata de la misma ubicación que la ruta de acceso física del sitio web proporcionada a IIS. El archivo *web.config* debe estar en la raíz de la aplicación para habilitar la publicación de varias aplicaciones mediante Web Deploy.

Los archivos confidenciales están en la ruta de acceso física de la aplicación, como *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (comentarios de documentación XML) y *\<assembly>.deps.json*. Si el archivo *web.config* está presente y el sitio se inicia normalmente, IIS no facilita estos archivos confidenciales, en el caso de que se soliciten. Si el archivo *web.config* no está presente, se le asignó un nombre incorrecto o no se puede configurar el sitio para un inicio normal, IIS puede servir archivos confidenciales públicamente.

**El archivo *web.config* debe estar presente en la implementación en todo momento, se le debe asignar un nombre correcto y debe ser capaz de configurar el sitio para el inicio normal. Nunca quite el archivo *web.config* de una implementación de producción.**

### <a name="transform-webconfig"></a>Transformación de web.config

Si necesita transformar *web.config* al realizar la publicación (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Configuración de IIS

**Sistemas operativos de servidor Windows**

Habilite el rol de servidor **Servidor web (IIS)** y establezca los servicios de rol.

1. Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**. En el paso **Roles de servidor**, active la casilla de **Servidor web (IIS)** .

   ![El rol Servidor web (IIS) se activa en el paso Seleccionar roles de servidor.](index/_static/server-roles-ws2016.png)

1. Después del paso **Características**, el paso **Servicios de rol** se carga para el servidor Web (IIS). Seleccione los servicios de rol IIS que quiera o acepte los servicios de rol predeterminados proporcionados.

   ![Los servicios de rol predeterminados se activan en el paso Seleccionar servicios de rol.](index/_static/role-services-ws2016.png)

   **Autenticación de Windows (opcional)**  
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servidor web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

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
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servicios World Wide Web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   WebSockets es compatible con ASP.NET Core 1.1 o posterior. Para habilitar WebSockets, expanda los nodos siguientes: **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**. Seleccione la característica **Protocolo WebSocket**. Para más información, vea [WebSockets](xref:fundamentals/websockets).

1. Si la instalación de IIS requiere un reinicio, reinicie el sistema.

![Consola de administración de IIS y Servicios World Wide Web se activan en Características de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalación del conjunto de hospedaje de .NET Core

Instale el *conjunto de hospedaje de .NET Core* en el sistema de hospedaje. El lote instala .NET Core Runtime, .NET Core Library y el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). El módulo permite que las aplicaciones ASP.NET Core se ejecuten detrás de IIS.

> [!IMPORTANT]
> Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto. Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.
>
> Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="download"></a>Descargar

1. Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
1. Seleccione la versión de .NET Core que quiera.
1. En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.
1. Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.

> [!WARNING]
> Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft. Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalación del conjunto de hospedaje

1. Ejecute el instalador en el servidor. Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:

   * `OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core. Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET). Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86. Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits. Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (*applicationHost.config*) está en la misma máquina que la instalación de IIS. *Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.* Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:

   ```console
   net stop was /y
   net start w3svc
   ```
   Al reiniciar IIS, se recoge un cambio en la variable PATH del sistema, que es una variable de entorno, realizado por el programa de instalación.

No es necesario detener manualmente los sitios individuales en IIS al instalar el conjunto de hospedaje. Las aplicaciones hospedadas (sitios de IIS) se reinician cuando IIS se reinicia. Las aplicaciones se inician de nuevo cuando reciben su primera solicitud, incluso desde el [módulo de inicialización de la aplicación](#application-initialization-module-and-idle-timeout).

ASP.NET Core adopta el comportamiento de puesta al día para las versiones de revisión de los paquetes de marco compartidos. Cuando las aplicaciones hospedadas por IIS se reinician con IIS, las aplicaciones se cargan con las versiones de revisión más recientes de los paquetes a los que se hace referencia cuando reciben su primera solicitud. Si IIS no se reinicia, las aplicaciones se reinician y muestran el comportamiento de la puesta al día cuando se reciclan sus procesos de trabajo y reciben su primera solicitud.

> [!NOTE]
> Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalación de Web Deploy al publicar con Visual Studio

Al implementar aplicaciones en servidores con [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale la versión más reciente de Web Deploy en el servidor. Para instalar Web Deploy, use el [Instalador de plataforma web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) u obtenga un instalador directamente desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). El método preferido es usar WebPI. WebPI ofrece una instalación independiente y una configuración para los proveedores de hospedaje.

## <a name="create-the-iis-site"></a>Creación del sitio de IIS

1. En el sistema de hospedaje, cree una carpeta para que contenga los archivos y las carpetas publicados de la aplicación. En un paso posterior, la ruta de acceso de la carpeta se proporciona a IIS como la ruta de acceso física a la aplicación. Para obtener más información sobre el diseño de carpetas y archivos de implementación de una aplicación, consulte <xref:host-and-deploy/directory-structure>.

1. En Administrador de IIS, abra el nodo del servidor en el panel **Conexiones**. Haga clic con el botón derecho en la carpeta **Sitios**. Haga clic en **Agregar sitio web** en el menú contextual.

1. Proporcione el **Nombre del sitio** y establezca la **Ruta de acceso física** a la carpeta de implementación de la aplicación. Proporcione la configuración de **Enlace** y cree el sitio web seleccionando **Aceptar**:

   ![Proporcione el nombre del sitio, la ruta de acceso física y el nombre de host en el paso Agregar sitio web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Los enlaces de carácter comodín de nivel superior (`http://*:80/` y `http://+:80`) **no** se deben usar. Los enlaces de carácter comodín de nivel superior pueden exponer su aplicación a vulnerabilidades de seguridad. Esto se aplica tanto a los caracteres comodín fuertes como a los débiles. Use nombres de host explícitos en lugar de caracteres comodín. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.mysub.com`) no suponen este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

1. En el nodo del servidor, seleccione **Grupos de aplicaciones**.

1. Haga clic con el botón derecho en el grupo de aplicaciones del sitio y seleccione **Configuración básica** en el menú contextual.

1. En la ventana **Modificar grupo de aplicaciones**, establezca **Versión de .NET CLR** en **Sin código administrado**:

   ![Establezca Sin código administrado para Versión de .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core se ejecuta en un proceso independiente y administra el runtime. ASP.NET Core no se basa en la carga de CLR de escritorio (.NET CLR); Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo. El establecimiento de **Versión de .NET CLR** en **Sin código administrado** es opcional, pero no es lo recomendable.

1. *ASP.NET Core 2.2 o posterior*: en el caso de las [implementaciones independientes](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) en las que se use un [modelo de hospedaje en proceso](#in-process-hosting-model), deshabilite el grupo de aplicaciones de los procesos de 32 bits (x86).

   En la barra lateral **Acciones** de la sección **Grupos de aplicaciones** de Administrador de IIS, seleccione **Establecer valores predeterminados de grupos de aplicaciones** o **Configuración avanzada**. Busque la opción **Habilitar aplicaciones de 32 bits** y establezca el valor en `False`. Las aplicaciones implementadas para un [hospedaje fuera de proceso](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model) no se ven afectadas por este ajuste.

1. Confirme que la identidad del modelo de proceso tiene los permisos adecuados.

   Si cambia la identidad predeterminada del grupo de aplicaciones (**Modelo de proceso** >  **Identity** ) de **ApplicationPoolIdentity** a otra identidad, compruebe que la nueva identidad tenga los permisos necesarios para acceder a la carpeta de la aplicación, la base de datos y otros recursos necesarios. Por ejemplo, el grupo de aplicaciones requiere acceso de lectura y escritura a las carpetas donde la aplicación lee y escribe archivos.

**Configuración de la autenticación de Windows (opcional)**  
Para más información, consulte [Configurar la autenticación de Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Implementación de la aplicación

Implemente la aplicación en la carpeta **Ruta de acceso física** de IIS que se creó en la sección [Creación del sitio de IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) es el mecanismo recomendado para la implementación, pero existen varias opciones para mover la aplicación desde la carpeta *publicar* del proyecto a la carpeta de implementación del sistema host.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy con Visual Studio

Vea el tema [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Perfiles de publicación de Visual Studio para la implementación de aplicaciones de ASP.NET Core) para obtener más información sobre cómo crear un perfil de publicación para su uso con Web Deploy. Si el proveedor de hospedaje proporciona un perfil de publicación o permite crear uno, descargue su perfil e impórtelo mediante el cuadro de diálogo **Publicar** de Visual Studio:

![Cuadro de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy fuera de Visual Studio

También puede usar [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) fuera de Visual Studio desde la línea de comandos. Para más información, vea [Web Deployment Tool (Herramienta de implementación web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativas a Web Deploy

Use cualquiera de los métodos disponibles para mover la aplicación al sistema de hospedaje, como copia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/).

Para obtener más información sobre ASP.NET Core en IIS, vea la sección [Recursos de implementación para administradores de IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Examinar el sitio web

Cuando la aplicación esté implementada en el sistema de hospedaje, realice una solicitud a uno de los puntos de conexión públicos de la aplicación.

En el ejemplo siguiente, el sitio se enlaza a un **Nombre de host** de IIS de `www.mysite.com` en el **Puerto** `80`. Se realiza una solicitud a `http://www.mysite.com`:

![El explorador Microsoft Edge ha cargado la página de inicio de IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Archivos de implementación bloqueados

Los archivos de la carpeta de implementación se bloquean cuando se ejecuta la aplicación. Los archivos bloqueados no se pueden sobrescribir durante la implementación. Para liberar archivos bloqueados de una implementación, detenga el grupo de aplicaciones mediante **uno** de los enfoques siguientes:

* Use Web Deploy con una referencia a `Microsoft.NET.Sdk.Web` en el archivo de proyecto. Se coloca un archivo *app_offline.htm* en la raíz del directorio de aplicación web. Cuando el archivo está presente, el módulo de ASP.NET Core cierra correctamente la aplicación y proporciona el archivo *app_offline.htm* durante la implementación. Para más información, vea [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm) (Referencia de configuración del módulo de ASP.NET Core).
* Detenga manualmente el grupo de aplicaciones en el Administrador de IIS en el servidor.
* Utilice PowerShell para colocar *app_offline.htm* (es necesario PowerShell 5 o una versión posterior):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Protección de datos

La [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) la usan varios [middlewares](xref:fundamentals/middleware/index) de ASP.NET Core, incluidos los que se emplean en la autenticación. Aunque el código de usuario no llame a las API de protección de datos, la protección de datos se debe configurar con un script de implementación o en el código de usuario para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente. Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.

Si el conjunto de claves se almacena en memoria cuando se reinicia la aplicación:

* Todos los tokens de autenticación basados en cookie se invalidan. 
* Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud. 
* Ya no se pueden descifrar los datos protegidos con el conjunto de claves. Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.

Para configurar la protección de datos en IIS para conservar el conjunto de claves, use **uno** de los enfoques siguientes:

* **Crear claves del Registro de protección de datos**

  Las claves de protección de datos que las aplicaciones de ASP.NET usan se almacenan en el Registro externo a las aplicaciones. Para conservar las claves de una determinada aplicación, cree claves del Registro para el grupo de aplicaciones.

  En las instalaciones independientes de IIS que no son de granja de servidores web, puede usar el [script de PowerShell Provision-AutoGenKeys.ps1 de protección de datos](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada grupo de aplicaciones usado con una aplicación de ASP.NET Core. Este script crea una clave del Registro en el registro HKLM que solo es accesible a la cuenta de proceso de trabajo del grupo de aplicaciones de la aplicación. Las claves se cifran en reposo mediante DPAPI con una clave de equipo.

  En escenarios de granja de servidores web, una aplicación puede configurarse para usar una ruta de acceso UNC para almacenar su conjunto de claves de protección de datos. De forma predeterminada, las claves de protección de datos no se cifran. Asegúrese de que los permisos de archivo de un recurso compartido de red se limitan a la cuenta de Windows bajo la que se ejecuta la aplicación. Puede usar un certificado X509 para proteger las claves en reposo. Considere un mecanismo que permita a los usuarios cargar certificados: coloque los certificados en el almacén de certificados de confianza del usuario y asegúrese de que están disponibles en todos los equipos en los que se ejecuta la aplicación del usuario. Vea [Configurar la protección de datos en ASP.NET Core](xref:security/data-protection/configuration/overview) para más información.

* **Configurar el grupo de aplicaciones de IIS para cargar el perfil de usuario**

  Esta opción está en la sección **Modelo de proceso**, en la **Configuración avanzada** del grupo de aplicaciones. Establezca **Cargar perfil de usuario** en `True`. Cuando se establece en `True`, las claves se almacenan en el directorio del perfil de usuario y se protegen mediante DPAPI con una clave específica de la cuenta de usuario. Las claves se conservan en la carpeta *%HOME%\ASP.NET\DataProtection-Keys*.

  También se debe habilitar el [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones. El valor predeterminado de `setProfileEnvironment` es `true`. En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`. Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:

  1. Vaya a la carpeta *%windir%/system32/inetsrv/config*.
  1. Abra el archivo *applicationHost.config*.
  1. Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.

* **Usar el sistema de archivos como un almacén de conjunto de claves**

  Ajuste el código de la aplicación para [usar el sistema de archivos como un almacén de conjunto de claves](xref:security/data-protection/configuration/overview). Use un certificado X509 para proteger el conjunto de claves y asegúrese de que sea un certificado de confianza. Si es un certificado autofirmado, colóquelo en el almacén raíz de confianza.

  Cuando se usa IIS en una granja de servidores web:

  * Use un recurso compartido de archivos al que puedan acceder todos los equipos.
  * Implemente un certificado X509 en cada equipo. Configure la [protección de datos en el código](xref:security/data-protection/configuration/overview).

* **Establecer una directiva para todo el equipo para la protección de datos**

  El sistema de protección de datos tiene compatibilidad limitada con el establecimiento de una [directiva de equipo](xref:security/data-protection/configuration/machine-wide-policy) predeterminada para todas las aplicaciones que usan las API de protección de datos. Para obtener más información, vea <xref:security/data-protection/introduction>.

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

1. En el cuadro de diálogo **Agregar aplicación**, use el botón **Seleccionar** en **Grupo de aplicaciones** para asignar el grupo de aplicaciones que ha creado para la aplicación secundaria. Seleccione **Aceptar**.

La asignación de un grupo de aplicaciones independiente de la aplicación secundaria es un requisito cuando se utiliza el modelo de hospedaje en proceso.

Para más información sobre el modelo de hospedaje en proceso y cómo configurar el módulo de ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Configuración de IIS con web.config

En escenarios de IIS que son funcionales para aplicaciones ASP.NET Core con el módulo ASP.NET Core, la configuración de IIS está influenciada por la sección `<system.webServer>` de *web.config*. Por ejemplo, la configuración de IIS es funcional para la compresión dinámica. Si IIS está configurado en el nivel de servidor para usar compresión dinámica, el elemento `<urlCompression>` del archivo *web.config* de la aplicación puede deshabilitarlo para una aplicación ASP.NET Core.

Para obtener más información, vea los temas siguientes:

* [Referencia de configuración para \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Para establecer variables de entorno para aplicaciones individuales que se ejecutan en grupos de aplicaciones aislados (compatible con IIS 10.0 o posterior), vea la sección *Comando AppCmd.exe* del tema [Variables de entorno\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) de la documentación de referencia de IIS.

## <a name="configuration-sections-of-webconfig"></a>Secciones de configuración de web.config

Las aplicaciones ASP.NET Core no usan las secciones de configuración de aplicaciones ASP.NET 4.x en *web.config* para la configuración:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Las aplicaciones de ASP.NET Core se configuran mediante otros proveedores de configuración. Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Grupos de aplicaciones

El aislamiento de los grupos de aplicaciones se determinan mediante el modelo de hospedaje:

* Hospedaje dentro de proceso: es necesario que las aplicaciones se ejecuten en grupos de aplicaciones distintos.
* Hospedaje fuera de proceso: nuestra recomendación es aislar las aplicaciones entre sí ejecutándolas en su propio grupo de aplicaciones.

El valor predeterminado del cuadro de diálogo **Agregar sitio web** de IIS es un único grupo de aplicaciones por aplicación. Cuando se proporciona el **Nombre del sitio**, el texto se transfiere automáticamente al cuadro de texto **Grupo de aplicaciones**. Al agregar el sitio se crea un grupo de aplicaciones con el nombre del sitio.

## <a name="application-pool-no-locidentity"></a>Identity del grupo de aplicaciones

Una cuenta de identidad del grupo de aplicaciones permite ejecutar una aplicación en una cuenta única sin tener que crear ni administrar dominios o cuentas locales. En IIS 8.0 o versiones posteriores, el proceso de trabajo de administración de IIS (WAS) crea una cuenta virtual con el nombre del nuevo grupo de aplicaciones y ejecuta los procesos de trabajo del grupo de aplicaciones en esta cuenta de forma predeterminada. En la Consola de administración de IIS, en la opción **Configuración avanzada** del grupo de aplicaciones, asegúrese de que **Identity** esté establecido para usar **ApplicationPoolIdentity** :

![Cuadro de diálogo Configuración avanzada del grupo de aplicaciones](index/_static/apppool-identity.png)

El proceso de administración de IIS crea un identificador seguro con el nombre del grupo de aplicaciones en el sistema de seguridad de Windows. Los recursos se pueden proteger mediante esta identidad. Sin embargo, no es una cuenta de usuario real ni se muestra en la consola de administración de usuario de Windows.

Si el proceso de trabajo de IIS requiere acceso con privilegios elevados a la aplicación, modifique la lista de control de acceso (ACL) del directorio que contiene la aplicación:

1. Abra el Explorador de Windows y vaya al directorio.

1. Haga clic con el botón derecho en el directorio y seleccione **Propiedades**.

1. En la pestaña **Seguridad**, haga clic en el botón **Editar** y en el botón **Agregar**.

1. Haga clic en el botón **Ubicaciones** y asegúrese de seleccionar el sistema.

1. Escriba **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** en el área **Escribir los nombres de objeto para seleccionar**. Haga clic en el botón **Comprobar nombres**. Para *DefaultAppPool* compruebe los nombres con **IIS AppPool\DefaultAppPool**. Cuando el botón **Comprobar nombres** está seleccionado, un valor de **DefaultAppPool** se indica en el área de los nombres de objeto. No es posible escribir el nombre del grupo de aplicaciones directamente en el área de los nombres de objeto. Use el formato **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** cuando compruebe el nombre del objeto.

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

Para una implementación en proceso cuando se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`. Para una implementación fuera de proceso cuando se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/1.1`.

Para obtener más información sobre los modelos de hospedaje en proceso y fuera de proceso, consulte <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 está habilitado de forma predeterminada. Las conexiones vuelven a HTTP/1.1 si no se establece una conexión HTTP/2. Para más información sobre la configuración HTTP/2 con implementaciones de IIS, vea [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Solicitudes CORS de preflight

*Esta sección solo se aplica a las aplicaciones de ASP.NET Core que tienen como destino .NET Framework.*

Para una aplicación de ASP.NET Core que tiene como destino .NET Framework, las solicitudes OPTIONS no se pasan a la aplicación de forma predeterminada en IIS. Para obtener información sobre cómo configurar los controladores de IIS de la aplicación en *web.config* para pasar las solicitudes OPTIONS, consulte [Habilitar solicitudes entre orígenes en ASP.NET Web API 2: Cómo funciona la CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Módulo de inicialización de aplicaciones y tiempo de espera de inactividad

Cuando se hospeda en IIS mediante la versión 2 del módulo de ASP.NET Core:

* [Módulo de inicialización de aplicaciones](#application-initialization-module): las aplicaciones hospedadas [dentro de proceso](#in-process-hosting-model) o [fuera de proceso](#out-of-process-hosting-model) se pueden configurar para iniciarse de forma automática en un reinicio de proceso de trabajo o un reinicio de servidor.
* [Tiempo de espera de inactividad](#idle-timeout): las aplicaciones hospedadas [dentro de proceso](#in-process-hosting-model) se pueden configurar para que no tengan tiempo de espera durante períodos de inactividad.

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
1. En el panel **Seleccionar servicios de rol**, abra el nodo **Desarrollo de aplicaciones**.
1. Active la casilla de **Inicialización de aplicaciones**.

Use cualquiera de los enfoques siguientes para habilitar el módulo de inicialización de aplicaciones para el sitio:

* Mediante el Administrador de IIS:

  1. Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.
  1. El valor predeterminado de **Modo de inicio** es **OnDemand**. Establezca **Modo de inicio** en **AlwaysRunning**. Seleccione **Aceptar**.
  1. Abra el nodo **Sitios** en el panel **Conexiones**.
  1. Haga clic con el botón derecho en la aplicación y seleccione **Administrar sitio web** > **Configuración avanzada**.
  1. El valor predeterminado de **Carga previa activada** es **False**. Establezca **Carga previa activada** en **True**. Seleccione **Aceptar**.

* Mediante *web.config*, agregue el elemento `<applicationInitialization>` con `doAppInitAfterRestart` establecido en `true` a los elementos `<system.webServer>` del archivo *web.config* de la aplicación:

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
1. El valor predeterminado de **Tiempo de inactividad (minutos)**  es **20** minutos. Establezca **Tiempo de inactividad (minutos)** en **0** (cero). Seleccione **Aceptar**.
1. Desactive y vuelva a activar el proceso de trabajo.

Para evitar que las aplicaciones hospedadas [fuera de proceso](#out-of-process-hosting-model) agoten el tiempo de espera, use cualquiera de los enfoques siguientes:

* Haga ping a la aplicación desde un servicio externo con el fin de mantenerla funcionando.
* Si la aplicación solo hospeda servicios en segundo plano, evite el hospedaje de IIS y use un [servicio de Windows para hospedar la aplicación de ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Recursos adicionales del módulo de inicialización de aplicaciones y del tiempo de espera de inactividad

* [Inicialización de aplicaciones IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicialización de aplicaciones \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).
* [Configuración del modelo de proceso para un grupo de aplicaciones \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Recursos de implementación para administradores de IIS

* [Documentación de IIS](/iis)
* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:index>
* [Sitio oficial de Microsoft IIS](https://www.iis.net/)
* [Biblioteca de contenido técnico de Windows Server](/windows-server/windows-server)
* [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para obtener una experiencia de tutorial en la publicación de una aplicación de ASP.NET Core en un servidor IIS, vea <xref:tutorials/publish-to-iis>.

[Instalación del conjunto de hospedaje de .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Los siguientes sistemas operativos son compatibles:

* Windows 7 o posterior
* Windows Server 2008 R2 o posterior

El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener) no funciona en una configuración de proxy inverso con IIS. Use el [servidor Kestrel](xref:fundamentals/servers/kestrel).

Para obtener información sobre el hospedaje en Azure, vea <xref:host-and-deploy/azure-apps/index>.

Para obtener instrucciones de solución de problemas, vea <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plataformas compatibles

Se admiten las aplicaciones publicadas para implementaciones de 32 bits (x86) y 64 bits (x64). Implemente una aplicación de 32 bits con un SDK de .NET Core de 32 bits (x86) a menos que la aplicación:

* Requiera el espacio de direcciones de memoria virtual más grande disponible para una aplicación de 64 bits.
* Requiera el tamaño de la pila IIS más grande.
* Tenga dependencias nativas de 64 bits.

Use un SDK de .NET Core de 64 bits (x64) para publicar una aplicación de 64 bits. Debe haber un tiempo de ejecución de 64 bits en el sistema host.

ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), un servidor HTTP multiplataforma predeterminado.

Al usar [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta en un proceso distinto al del proceso de trabajo de IIS (*fuera de proceso*) con el [servidor Kestrel](xref:fundamentals/servers/index#kestrel).

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:

![Módulo ASP.NET Core](index/_static/ancm-outofprocess.png)

Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS). El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.

`CreateDefaultBuilder` configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y habilita IIS Integration mediante la configuración de la ruta de acceso y el puerto base para el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

El módulo ASP.NET Core genera un puerto dinámico que se asigna al proceso back-end. `CreateDefaultBuilder` llama al método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>. `UseIISIntegration` configura Kestrel para escuchar en el puerto dinámico en la dirección IP de localhost (`127.0.0.1`). Si el puerto dinámico es 1234, Kestrel escucha en `127.0.0.1:1234`. Esta configuración reemplaza a otras configuraciones de dirección URL proporcionadas por:

* `UseUrls`
* [API de escucha de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Configuración](xref:fundamentals/configuration/index) (u [opción --urls de la línea de comandos](xref:fundamentals/host/web-host#override-configuration))

No es necesario realizar llamadas a `UseUrls` o a la API `Listen` de Kestrel cuando se usa el módulo. Si se llama a `UseUrls` o `Listen`, Kestrel escucha en el puerto especificado cuando se ejecuta la aplicación sin IIS.

Para obtener instrucciones sobre la configuración del módulo ASP.NET Core, vea <xref:host-and-deploy/aspnet-core-module>.

Para obtener más información sobre el hospedaje, consulte [Hospedaje en ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuración de aplicación

### <a name="enable-the-iisintegration-components"></a>Habilitación de los componentes de integración con IIS

Al compilar un host en `CreateWebHostBuilder` (*Program.cs*), llame a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para permitir la integración de IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Para más información sobre `CreateDefaultBuilder`, consulte <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Opciones de IIS

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el servidor IIS establece el `HttpContext.User` autenticado mediante la [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el servidor solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para obtener más información, vea [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |

Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. En el ejemplo siguiente se impide que la aplicación rellene `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el [middleware de integración con IIS](#enable-the-iisintegration-components) establece el `HttpContext.User` autenticado mediante [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el middleware solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para más información, consulte el tema [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `ForwardClientCertificate`     | `true`  | Si `HttpContext.Connection.ClientCertificate` y el encabezado de solicitud `true` está presente, se rellena `MS-ASPNETCORE-CLIENTCERT`. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

El [middleware de integración con IIS](#enable-the-iisintegration-components), que configura el software intermedio de encabezados reenviados, y el módulo de ASP.NET Core están configurados para reenviar el esquema (HTTP/HTTPS) y la dirección IP remota donde se originó la solicitud. Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Archivo web.config

El archivo *web.config* configura el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). La creación, transformación y publicación del archivo *web.config* se controla por medio de un destino de MSBuild (`_TransformWebConfig`) cuando el proyecto se publica. Este destino está incluido entre los destinos del SDK web (`Microsoft.NET.Sdk.Web`). El SDK se establece al inicio del archivo del proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el proyecto no incluye un archivo *web.config*, el archivo se crea con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la [salida publicada](xref:host-and-deploy/directory-structure).

Si el proyecto incluye un archivo *web.config*, el archivo se transforma con los elementos *processPath* y *arguments* correctos para configurar el módulo ASP.NET Core y se mueve a la salida publicada. La transformación no modifica los valores de configuración de IIS del archivo.

El archivo *web.config* puede proporcionar valores de configuración de IIS adicionales que controlan los módulos activos de IIS. Para información sobre los módulos de IIS que son capaces de procesar las solicitudes con aplicaciones ASP.NET Core, vea el tema [Módulos IIS](xref:host-and-deploy/iis/modules).

Para evitar que el SDK web transforme el archivo *web.config*, use la propiedad **\<IsTransformWebConfigDisabled>** en el archivo del proyecto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Al deshabilitar el SDK web para la transformación del archivo, el desarrollador debe establecer el elemento *processPath* y los *argumentos* manualmente. Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Ubicación del archivo web.config

Para configurar el [módulo ASP.NET](xref:host-and-deploy/aspnet-core-module) correctamente, el archivo *web.config* debe estar presente en la ruta [raíz de contenido](xref:fundamentals/index#content-root) (normalmente la ruta de acceso base de la aplicación) de la aplicación implementada. Se trata de la misma ubicación que la ruta de acceso física del sitio web proporcionada a IIS. El archivo *web.config* debe estar en la raíz de la aplicación para habilitar la publicación de varias aplicaciones mediante Web Deploy.

Los archivos confidenciales están en la ruta de acceso física de la aplicación, como *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (comentarios de documentación XML) y *\<assembly>.deps.json*. Si el archivo *web.config* está presente y el sitio se inicia normalmente, IIS no facilita estos archivos confidenciales, en el caso de que se soliciten. Si el archivo *web.config* no está presente, se le asignó un nombre incorrecto o no se puede configurar el sitio para un inicio normal, IIS puede servir archivos confidenciales públicamente.

**El archivo *web.config* debe estar presente en la implementación en todo momento, se le debe asignar un nombre correcto y debe ser capaz de configurar el sitio para el inicio normal. Nunca quite el archivo *web.config* de una implementación de producción.**

### <a name="transform-webconfig"></a>Transformación de web.config

Si necesita transformar *web.config* al realizar la publicación (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Configuración de IIS

**Sistemas operativos de servidor Windows**

Habilite el rol de servidor **Servidor web (IIS)** y establezca los servicios de rol.

1. Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**. En el paso **Roles de servidor**, active la casilla de **Servidor web (IIS)** .

   ![El rol Servidor web (IIS) se activa en el paso Seleccionar roles de servidor.](index/_static/server-roles-ws2016.png)

1. Después del paso **Características**, el paso **Servicios de rol** se carga para el servidor Web (IIS). Seleccione los servicios de rol IIS que quiera o acepte los servicios de rol predeterminados proporcionados.

   ![Los servicios de rol predeterminados se activan en el paso Seleccionar servicios de rol.](index/_static/role-services-ws2016.png)

   **Autenticación de Windows (opcional)**  
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servidor web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

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
   Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servicios World Wide Web** > **Seguridad**. Seleccione la característica **Autenticación de Windows**. Para más información, consulte [Autenticación de Windows \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   WebSockets es compatible con ASP.NET Core 1.1 o posterior. Para habilitar WebSockets, expanda los nodos siguientes: **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**. Seleccione la característica **Protocolo WebSocket**. Para más información, vea [WebSockets](xref:fundamentals/websockets).

1. Si la instalación de IIS requiere un reinicio, reinicie el sistema.

![Consola de administración de IIS y Servicios World Wide Web se activan en Características de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalación del conjunto de hospedaje de .NET Core

Instale el *conjunto de hospedaje de .NET Core* en el sistema de hospedaje. El lote instala .NET Core Runtime, .NET Core Library y el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). El módulo permite que las aplicaciones ASP.NET Core se ejecuten detrás de IIS.

> [!IMPORTANT]
> Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto. Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.
>
> Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="download"></a>Descargar

1. Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
1. Seleccione la versión de .NET Core que quiera.
1. En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.
1. Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.

> [!WARNING]
> Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft. Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalación del conjunto de hospedaje

1. Ejecute el instalador en el servidor. Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:

   * `OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core. Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET). Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86. Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits. Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (*applicationHost.config*) está en la misma máquina que la instalación de IIS. *Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.* Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:

   ```console
   net stop was /y
   net start w3svc
   ```
   Al reiniciar IIS, se recoge un cambio en la variable PATH del sistema, que es una variable de entorno, realizado por el programa de instalación.

No es necesario detener manualmente los sitios individuales en IIS al instalar el conjunto de hospedaje. Las aplicaciones hospedadas (sitios de IIS) se reinician cuando IIS se reinicia. Las aplicaciones se inician de nuevo cuando reciben su primera solicitud, incluso desde el [módulo de inicialización de la aplicación](#application-initialization-module-and-idle-timeout).

ASP.NET Core adopta el comportamiento de puesta al día para las versiones de revisión de los paquetes de marco compartidos. Cuando las aplicaciones hospedadas por IIS se reinician con IIS, las aplicaciones se cargan con las versiones de revisión más recientes de los paquetes a los que se hace referencia cuando reciben su primera solicitud. Si IIS no se reinicia, las aplicaciones se reinician y muestran el comportamiento de la puesta al día cuando se reciclan sus procesos de trabajo y reciben su primera solicitud.

> [!NOTE]
> Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalación de Web Deploy al publicar con Visual Studio

Al implementar aplicaciones en servidores con [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), instale la versión más reciente de Web Deploy en el servidor. Para instalar Web Deploy, use el [Instalador de plataforma web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) u obtenga un instalador directamente desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). El método preferido es usar WebPI. WebPI ofrece una instalación independiente y una configuración para los proveedores de hospedaje.

## <a name="create-the-iis-site"></a>Creación del sitio de IIS

1. En el sistema de hospedaje, cree una carpeta para que contenga los archivos y las carpetas publicados de la aplicación. En un paso posterior, la ruta de acceso de la carpeta se proporciona a IIS como la ruta de acceso física a la aplicación. Para obtener más información sobre el diseño de carpetas y archivos de implementación de una aplicación, consulte <xref:host-and-deploy/directory-structure>.

1. En Administrador de IIS, abra el nodo del servidor en el panel **Conexiones**. Haga clic con el botón derecho en la carpeta **Sitios**. Haga clic en **Agregar sitio web** en el menú contextual.

1. Proporcione el **Nombre del sitio** y establezca la **Ruta de acceso física** a la carpeta de implementación de la aplicación. Proporcione la configuración de **Enlace** y cree el sitio web seleccionando **Aceptar**:

   ![Proporcione el nombre del sitio, la ruta de acceso física y el nombre de host en el paso Agregar sitio web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Los enlaces de carácter comodín de nivel superior (`http://*:80/` y `http://+:80`) **no** se deben usar. Los enlaces de carácter comodín de nivel superior pueden exponer su aplicación a vulnerabilidades de seguridad. Esto se aplica tanto a los caracteres comodín fuertes como a los débiles. Use nombres de host explícitos en lugar de caracteres comodín. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.mysub.com`) no suponen este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

1. En el nodo del servidor, seleccione **Grupos de aplicaciones**.

1. Haga clic con el botón derecho en el grupo de aplicaciones del sitio y seleccione **Configuración básica** en el menú contextual.

1. En la ventana **Modificar grupo de aplicaciones**, establezca **Versión de .NET CLR** en **Sin código administrado**:

   ![Establezca Sin código administrado para Versión de .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core se ejecuta en un proceso independiente y administra el runtime. ASP.NET Core no se basa en la carga de CLR de escritorio (.NET CLR); Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo. El establecimiento de **Versión de .NET CLR** en **Sin código administrado** es opcional, pero no es lo recomendable.

1. *ASP.NET Core 2.2 o posterior*: en el caso de las [implementaciones independientes](/dotnet/core/deploying/#self-contained-deployments-scd) de 64 bits (x64) en las que se use un [modelo de hospedaje en proceso](#in-process-hosting-model), deshabilite el grupo de aplicaciones de los procesos de 32 bits (x86).

   En la barra lateral **Acciones** de la sección **Grupos de aplicaciones** de Administrador de IIS, seleccione **Establecer valores predeterminados de grupos de aplicaciones** o **Configuración avanzada**. Busque la opción **Habilitar aplicaciones de 32 bits** y establezca el valor en `False`. Las aplicaciones implementadas para un [hospedaje fuera de proceso](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model) no se ven afectadas por este ajuste.

1. Confirme que la identidad del modelo de proceso tiene los permisos adecuados.

   Si cambia la identidad predeterminada del grupo de aplicaciones (**Modelo de proceso** >  **Identity** ) de **ApplicationPoolIdentity** a otra identidad, compruebe que la nueva identidad tenga los permisos necesarios para acceder a la carpeta de la aplicación, la base de datos y otros recursos necesarios. Por ejemplo, el grupo de aplicaciones requiere acceso de lectura y escritura a las carpetas donde la aplicación lee y escribe archivos.

**Configuración de la autenticación de Windows (opcional)**  
Para más información, consulte [Configurar la autenticación de Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Implementación de la aplicación

Implemente la aplicación en la carpeta **Ruta de acceso física** de IIS que se creó en la sección [Creación del sitio de IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) es el mecanismo recomendado para la implementación, pero existen varias opciones para mover la aplicación desde la carpeta *publicar* del proyecto a la carpeta de implementación del sistema host.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy con Visual Studio

Vea el tema [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Perfiles de publicación de Visual Studio para la implementación de aplicaciones de ASP.NET Core) para obtener más información sobre cómo crear un perfil de publicación para su uso con Web Deploy. Si el proveedor de hospedaje proporciona un perfil de publicación o permite crear uno, descargue su perfil e impórtelo mediante el cuadro de diálogo **Publicar** de Visual Studio:

![Cuadro de diálogo Publicar](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy fuera de Visual Studio

También puede usar [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) fuera de Visual Studio desde la línea de comandos. Para más información, vea [Web Deployment Tool (Herramienta de implementación web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativas a Web Deploy

Use cualquiera de los métodos disponibles para mover la aplicación al sistema de hospedaje, como copia manual, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/).

Para obtener más información sobre ASP.NET Core en IIS, vea la sección [Recursos de implementación para administradores de IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Examinar el sitio web

Cuando la aplicación esté implementada en el sistema de hospedaje, realice una solicitud a uno de los puntos de conexión públicos de la aplicación.

En el ejemplo siguiente, el sitio se enlaza a un **Nombre de host** de IIS de `www.mysite.com` en el **Puerto** `80`. Se realiza una solicitud a `http://www.mysite.com`:

![El explorador Microsoft Edge ha cargado la página de inicio de IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Archivos de implementación bloqueados

Los archivos de la carpeta de implementación se bloquean cuando se ejecuta la aplicación. Los archivos bloqueados no se pueden sobrescribir durante la implementación. Para liberar archivos bloqueados de una implementación, detenga el grupo de aplicaciones mediante **uno** de los enfoques siguientes:

* Use Web Deploy con una referencia a `Microsoft.NET.Sdk.Web` en el archivo de proyecto. Se coloca un archivo *app_offline.htm* en la raíz del directorio de aplicación web. Cuando el archivo está presente, el módulo de ASP.NET Core cierra correctamente la aplicación y proporciona el archivo *app_offline.htm* durante la implementación. Para más información, vea [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm) (Referencia de configuración del módulo de ASP.NET Core).
* Detenga manualmente el grupo de aplicaciones en el Administrador de IIS en el servidor.
* Utilice PowerShell para colocar *app_offline.htm* (es necesario PowerShell 5 o una versión posterior):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Protección de datos

La [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) la usan varios [middlewares](xref:fundamentals/middleware/index) de ASP.NET Core, incluidos los que se emplean en la autenticación. Aunque el código de usuario no llame a las API de protección de datos, la protección de datos se debe configurar con un script de implementación o en el código de usuario para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente. Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.

Si el conjunto de claves se almacena en memoria cuando se reinicia la aplicación:

* Todos los tokens de autenticación basados en cookie se invalidan. 
* Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud. 
* Ya no se pueden descifrar los datos protegidos con el conjunto de claves. Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.

Para configurar la protección de datos en IIS para conservar el conjunto de claves, use **uno** de los enfoques siguientes:

* **Crear claves del Registro de protección de datos**

  Las claves de protección de datos que las aplicaciones de ASP.NET usan se almacenan en el Registro externo a las aplicaciones. Para conservar las claves de una determinada aplicación, cree claves del Registro para el grupo de aplicaciones.

  En las instalaciones independientes de IIS que no son de granja de servidores web, puede usar el [script de PowerShell Provision-AutoGenKeys.ps1 de protección de datos](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada grupo de aplicaciones usado con una aplicación de ASP.NET Core. Este script crea una clave del Registro en el registro HKLM que solo es accesible a la cuenta de proceso de trabajo del grupo de aplicaciones de la aplicación. Las claves se cifran en reposo mediante DPAPI con una clave de equipo.

  En escenarios de granja de servidores web, una aplicación puede configurarse para usar una ruta de acceso UNC para almacenar su conjunto de claves de protección de datos. De forma predeterminada, las claves de protección de datos no se cifran. Asegúrese de que los permisos de archivo de un recurso compartido de red se limitan a la cuenta de Windows bajo la que se ejecuta la aplicación. Puede usar un certificado X509 para proteger las claves en reposo. Considere un mecanismo que permita a los usuarios cargar certificados: coloque los certificados en el almacén de certificados de confianza del usuario y asegúrese de que están disponibles en todos los equipos en los que se ejecuta la aplicación del usuario. Vea [Configurar la protección de datos en ASP.NET Core](xref:security/data-protection/configuration/overview) para más información.

* **Configurar el grupo de aplicaciones de IIS para cargar el perfil de usuario**

  Esta opción está en la sección **Modelo de proceso**, en la **Configuración avanzada** del grupo de aplicaciones. Establezca **Cargar perfil de usuario** en `True`. Cuando se establece en `True`, las claves se almacenan en el directorio del perfil de usuario y se protegen mediante DPAPI con una clave específica de la cuenta de usuario. Las claves se conservan en la carpeta *%HOME%\ASP.NET\DataProtection-Keys*.

  También se debe habilitar el [atributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones. El valor predeterminado de `setProfileEnvironment` es `true`. En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`. Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:

  1. Vaya a la carpeta *%windir%/system32/inetsrv/config*.
  1. Abra el archivo *applicationHost.config*.
  1. Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.

* **Usar el sistema de archivos como un almacén de conjunto de claves**

  Ajuste el código de la aplicación para [usar el sistema de archivos como un almacén de conjunto de claves](xref:security/data-protection/configuration/overview). Use un certificado X509 para proteger el conjunto de claves y asegúrese de que sea un certificado de confianza. Si es un certificado autofirmado, colóquelo en el almacén raíz de confianza.

  Cuando se usa IIS en una granja de servidores web:

  * Use un recurso compartido de archivos al que puedan acceder todos los equipos.
  * Implemente un certificado X509 en cada equipo. Configure la [protección de datos en el código](xref:security/data-protection/configuration/overview).

* **Establecer una directiva para todo el equipo para la protección de datos**

  El sistema de protección de datos tiene compatibilidad limitada con el establecimiento de una [directiva de equipo](xref:security/data-protection/configuration/machine-wide-policy) predeterminada para todas las aplicaciones que usan las API de protección de datos. Para obtener más información, vea <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Directorios virtuales

Los [directorios virtuales de IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) no son compatibles con aplicaciones ASP.NET Core. Una aplicación puede hospedarse como [subaplicación](#sub-applications).

## <a name="sub-applications"></a>Subaplicaciones

Se puede hospedar una aplicación ASP.NET Core como una [subaplicación IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). La ruta de acceso de la subaplicación se convierte en parte de la dirección URL de la aplicación raíz.

Una aplicación secundaria no debe incluir el módulo de ASP.NET Core como controlador. Si se agrega el módulo como controlador al archivo *web.config* de una aplicación secundaria, aparece un *error 500.19 (Error interno del servidor)* que hace referencia al archivo de configuración erróneo al intentar examinar la aplicación secundaria.

En el ejemplo siguiente se muestra un archivo *web.config* publicado para una aplicación secundaria ASP.NET Core:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Al hospedar una aplicación secundaria que no es de ASP.NET Core bajo una aplicación de ASP.NET Core, quite explícitamente el controlador heredado del archivo *web.config* de la aplicación secundaria:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Los vínculos a los recursos estáticos dentro de la aplicación secundaria deben utilizar una notación de una tilde con una barra diagonal (`~/`). La notación de tilde barra diagonal desencadena un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) para anteponer la ruta de acceso de la aplicación secundaria al vínculo relativo representado. Para una aplicación secundaria en `/subapp_path`, una imagen vinculada con `src="~/image.png"` se representa como `src="/subapp_path/image.png"`. El middleware de archivos estáticos de la aplicación raíz no procesa la solicitud de archivo estático. La solicitud se procesa mediante el middleware de archivos estáticos de la aplicación secundaria.

Si el atributo `src` de un recurso estático se establece en una ruta de acceso absoluta (por ejemplo, `src="/image.png"`), el vínculo se representa sin la base de la ruta de acceso de la aplicación secundaria. El middleware de archivos estáticos de la aplicación raíz intenta atender al recurso desde el [web root](xref:fundamentals/index#web-root), lo que resulta en una respuesta *404 - No encontrado* a menos que el recurso estático esté disponible desde la aplicación raíz.

Para hospedar una aplicación ASP.NET Core como aplicación secundaria en otra aplicación ASP.NET Core:

1. Establezca un grupo de aplicaciones para la aplicación secundaria. Establezca **Versión de .NET CLR**  en **Sin código administrado**  porque Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo, no el CLR de escritorio (.NET CLR).

1. Agregue el sitio raíz en el Administrador de IIS con la aplicación secundaria en una carpeta en el sitio raíz.

1. Haga clic con el botón derecho en la carpeta de la aplicación secundaria en el Administrador de IIS y seleccione **Convertir en aplicación**.

1. En el cuadro de diálogo **Agregar aplicación**, use el botón **Seleccionar** en **Grupo de aplicaciones** para asignar el grupo de aplicaciones que ha creado para la aplicación secundaria. Seleccione **Aceptar**.

La asignación de un grupo de aplicaciones independiente de la aplicación secundaria es un requisito cuando se utiliza el modelo de hospedaje en proceso.

Para más información sobre el modelo de hospedaje en proceso y cómo configurar el módulo de ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Configuración de IIS con web.config

En escenarios de IIS que son funcionales para aplicaciones ASP.NET Core con el módulo ASP.NET Core, la configuración de IIS está influenciada por la sección `<system.webServer>` de *web.config*. Por ejemplo, la configuración de IIS es funcional para la compresión dinámica. Si IIS está configurado en el nivel de servidor para usar compresión dinámica, el elemento `<urlCompression>` del archivo *web.config* de la aplicación puede deshabilitarlo para una aplicación ASP.NET Core.

Para obtener más información, vea los temas siguientes:

* [Referencia de configuración para \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Para establecer variables de entorno para aplicaciones individuales que se ejecutan en grupos de aplicaciones aislados (compatible con IIS 10.0 o posterior), vea la sección *Comando AppCmd.exe* del tema [Variables de entorno\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) de la documentación de referencia de IIS.

## <a name="configuration-sections-of-webconfig"></a>Secciones de configuración de web.config

Las aplicaciones ASP.NET Core no usan las secciones de configuración de aplicaciones ASP.NET 4.x en *web.config* para la configuración:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Las aplicaciones de ASP.NET Core se configuran mediante otros proveedores de configuración. Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Grupos de aplicaciones

Al hospedar varios sitios web en un servidor, nuestra recomendación es aislar las aplicaciones entre sí ejecutándolas en su propio grupo de aplicaciones. El valor predeterminado del cuadro de diálogo **Agregar sitio web** es esta configuración. Cuando se proporciona el **Nombre del sitio**, el texto se transfiere automáticamente al cuadro de texto **Grupo de aplicaciones**. Al agregar el sitio se crea un grupo de aplicaciones con el nombre del sitio.

## <a name="application-pool-no-locidentity"></a>Identity del grupo de aplicaciones

Una cuenta de identidad del grupo de aplicaciones permite ejecutar una aplicación en una cuenta única sin tener que crear ni administrar dominios o cuentas locales. En IIS 8.0 o versiones posteriores, el proceso de trabajo de administración de IIS (WAS) crea una cuenta virtual con el nombre del nuevo grupo de aplicaciones y ejecuta los procesos de trabajo del grupo de aplicaciones en esta cuenta de forma predeterminada. En la Consola de administración de IIS, en la opción **Configuración avanzada** del grupo de aplicaciones, asegúrese de que **Identity** esté establecido para usar **ApplicationPoolIdentity** :

![Cuadro de diálogo Configuración avanzada del grupo de aplicaciones](index/_static/apppool-identity.png)

El proceso de administración de IIS crea un identificador seguro con el nombre del grupo de aplicaciones en el sistema de seguridad de Windows. Los recursos se pueden proteger mediante esta identidad. Sin embargo, no es una cuenta de usuario real ni se muestra en la consola de administración de usuario de Windows.

Si el proceso de trabajo de IIS requiere acceso con privilegios elevados a la aplicación, modifique la lista de control de acceso (ACL) del directorio que contiene la aplicación:

1. Abra el Explorador de Windows y vaya al directorio.

1. Haga clic con el botón derecho en el directorio y seleccione **Propiedades**.

1. En la pestaña **Seguridad**, haga clic en el botón **Editar** y en el botón **Agregar**.

1. Haga clic en el botón **Ubicaciones** y asegúrese de seleccionar el sistema.

1. Escriba **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** en el área **Escribir los nombres de objeto para seleccionar**. Haga clic en el botón **Comprobar nombres**. Para *DefaultAppPool* compruebe los nombres con **IIS AppPool\DefaultAppPool**. Cuando el botón **Comprobar nombres** está seleccionado, un valor de **DefaultAppPool** se indica en el área de los nombres de objeto. No es posible escribir el nombre del grupo de aplicaciones directamente en el área de los nombres de objeto. Use el formato **IIS AppPool\\<nombre_del_grupo_de_aplicaciones>** cuando compruebe el nombre del objeto.

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

[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con las implementaciones fuera de proceso que cumplen los requisitos básicos siguientes:

* Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
* Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso al [servidor de Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.
* Marco de destino: no es aplicable a las implementaciones fuera de proceso, ya que IIS controla completamente la conexión HTTP/2.
* Conexión con TLS 1.2 o una versión posterior

Si se establece una conexión HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/1.1`.

HTTP/2 está habilitado de forma predeterminada. Las conexiones vuelven a HTTP/1.1 si no se establece una conexión HTTP/2. Para más información sobre la configuración HTTP/2 con implementaciones de IIS, vea [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Solicitudes CORS de preflight

*Esta sección solo se aplica a las aplicaciones de ASP.NET Core que tienen como destino .NET Framework.*

Para una aplicación de ASP.NET Core que tiene como destino .NET Framework, las solicitudes OPTIONS no se pasan a la aplicación de forma predeterminada en IIS. Para obtener información sobre cómo configurar los controladores de IIS de la aplicación en *web.config* para pasar las solicitudes OPTIONS, consulte [Habilitar solicitudes entre orígenes en ASP.NET Web API 2: Cómo funciona la CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Recursos de implementación para administradores de IIS

* [Documentación de IIS](/iis)
* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:index>
* [Sitio oficial de Microsoft IIS](https://www.iis.net/)
* [Biblioteca de contenido técnico de Windows Server](/windows-server/windows-server)
* [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
