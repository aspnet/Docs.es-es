---
title: Módulo ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar el módulo de ASP.NET Core para hospedar aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c5c26c72af8103167b421cfa4aa06c23c242c1d7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015912"
---
# <a name="aspnet-core-module"></a>Módulo ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) y [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:

* Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).
* Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).

Versiones de Windows compatibles:

* Windows 7 o posterior
* Windows Server 2012 R2 o versiones posteriores

En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.

Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel. El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modelos de hospedaje

### <a name="in-process-hosting-model"></a>Modelo de hospedaje en proceso

Las aplicaciones ASP.NET Core usan de forma predeterminada el modelo de hospedaje dentro de proceso.

Al hospedar en proceso, se aplican las siguientes características:

* En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel). En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:

  * Registrar el `IISHttpServer`.
  * Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
  * Configurar el host para capturar errores de inicio.

* El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.

* No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones. Se usa un grupo de aplicaciones por aplicación.

* Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta. Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.

* La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.

* Se detectan las desconexiones del cliente. El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.

* En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).

  Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Llame al método `SetCurrentDirectory`. Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.

* Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario. Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada. Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).

### <a name="out-of-process-hosting-model"></a>Modelo de hospedaje fuera de proceso

Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( *.csproj*):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.

El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.

En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).

Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:

* Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
* Configurar el host para capturar errores de inicio.

### <a name="hosting-model-changes"></a>Cambios del modelo de hospedaje

Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.

En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual. La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.

### <a name="process-name"></a>Nombre del proceso

`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).

Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos. Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.

El módulo ASP.NET Core también puede:

* Establecer variables de entorno para un proceso de trabajo.
* Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.
* Reenviar tokens de autenticación de Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Cómo instalar y usar el módulo ASP.NET Core

Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuración con web.config

El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.

El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).

Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`. La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.

Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos del elemento aspNetCore

| Atributo | Descripción | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadena opcional.</p><p>Argumentos para el archivo ejecutable especificado en **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Atributo Boolean opcional.</p><p>Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo Boolean opcional.</p><p>Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud. Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</p> | `true` |
| `hostingModel` | <p>Atributo de cadena opcional.</p><p>Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Atributo integer opcional.</p><p>Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</p><p>&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</p><p>No se recomienda establecer `processesPerApplication`. Este atributo se quitará en futuras versiones.</p> | Valor predeterminado: `1`<br>Mínimo: `1`<br>Máximo: `100`&dagger; |
| `processPath` | <p>Atributo de cadena necesario.</p><p>Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP. No se admiten rutas de acceso relativas. Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</p> | |
| `rapidFailsPerMinute` | <p>Atributo integer opcional.</p><p>Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto. Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</p><p>No admitido con el hospedaje en proceso.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `100` |
| `requestTimeout` | <p>Atributo timespan opcional.</p><p>Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</p><p>En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</p><p>No se aplica al hospedaje en proceso. En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</p><p>Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59. El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</p> | Valor predeterminado: `00:02:00`<br>Mínimo: `00:00:00`<br>Máximo: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `600` |
| `startupTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto. Si se supera este límite de tiempo, el módulo termina el proceso.</p><p>Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración **rapidFailsPerMinute**.</p><p>Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</p><p>Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</p> | Valor predeterminado: `120`<br>Mínimo: `0`<br>Máximo: `3600` |
| `stdoutLogEnabled` | <p>Atributo Boolean opcional.</p><p>Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadena opcional.</p><p>Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**. Las rutas de acceso relativas son relativas a la raíz del sitio. Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas. Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso. Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**. Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Establecimiento de las variables de entorno

Se pueden especificar variables de entorno para el proceso en el atributo `processPath`. Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`. Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.

El ejemplo siguiente establece dos variables de entorno en *web.config*. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación en `Development`. Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación. `CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto. Este método establece el entorno en *web.config* cuando se publica el proyecto:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes. Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.

Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*. Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.

Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta. Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.

## <a name="start-up-error-page"></a>Página de errores de inicio

Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.

Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.

Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.

En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.

Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`. Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Creación y redireccionamiento de registros

El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`. Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`. El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).

Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso. Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.

Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.

No use el registro de stdout con fines de registro de aplicaciones general. Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo. El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado. Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.

Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB. Después del inicio, se descartarán los registros adicionales.

En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`. Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`. La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.

Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.

Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Registros de diagnóstico mejorados

El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados. Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (*logs* en el ejemplo anterior). El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).

Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.

Niveles (en orden de menos a más detallado):

* ERROR
* WARNING
* INFO
* TRACE

Ubicaciones (se permiten varias ubicaciones):

* CONSOLE
* EVENTLOG
* ARCHIVO

También se puede proporcionar la configuración de controlador a través de variables de entorno:

* `ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración. (El valor predeterminado es *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.

> [!WARNING]
> **No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema. El tamaño del registro no es limitado. Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.

Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.

## <a name="modify-the-stack-size"></a>Modificación del tamaño de la pila

*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*

Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en *web.config*. El tamaño predeterminado es `1048576` bytes (1 MB).

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

El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**. Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.

Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:

1. Deshabilite la configuración compartida de IIS.
1. Ejecute el instalador.
1. Exporte el archivo *applicationHost.config* actualizado al recurso compartido.
1. Vuelva a habilitar la configuración compartida de IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versión del módulo y registros del instalador de la agrupación de hospedaje

Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:

1. En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.
1. Busque el archivo *aspnetcore.dll*.
1. Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.
1. Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.

Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Ubicaciones del módulo, el esquema y el archivo de configuración

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Configuración

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:

* Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).
* Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).

Versiones de Windows compatibles:

* Windows 7 o posterior
* Windows Server 2008 R2 o posterior

En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.

Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel. El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modelos de hospedaje

### <a name="in-process-hosting-model"></a>Modelo de hospedaje en proceso

Para configurar una aplicación para el hospedaje en proceso, agregue la propiedad `<AspNetCoreHostingModel>` al archivo de proyecto de la aplicación con un valor de `InProcess` (el hospedaje fuera de proceso se establece con `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

No se admite el modelo de hospedaje en proceso para aplicaciones de ASP.NET Core que tienen como destino .NET Framework.

El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.

Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.

Al hospedar en proceso, se aplican las siguientes características:

* En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel). En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:

  * Registrar el `IISHttpServer`.
  * Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
  * Configurar el host para capturar errores de inicio.

* El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.

* No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones. Se usa un grupo de aplicaciones por aplicación.

* Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta. Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.

* La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.

* Se detectan las desconexiones del cliente. El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.

* En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).

  Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Llame al método `SetCurrentDirectory`. Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.

* Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario. Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada. Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Modelo de hospedaje fuera de proceso

Para configurar una aplicación para el hospedaje fuera de proceso, use uno de los métodos siguientes en el archivo de proyecto:

* No especifique la propiedad `<AspNetCoreHostingModel>`. Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.
* Establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` (el hospedaje en proceso se establece con `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

El valor no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.

En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).

Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:

* Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
* Configurar el host para capturar errores de inicio.

### <a name="hosting-model-changes"></a>Cambios del modelo de hospedaje

Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.

En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual. La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.

### <a name="process-name"></a>Nombre del proceso

`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).

Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos. Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.

El módulo ASP.NET Core también puede:

* Establecer variables de entorno para un proceso de trabajo.
* Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.
* Reenviar tokens de autenticación de Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Cómo instalar y usar el módulo ASP.NET Core

Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuración con web.config

El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.

El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).

Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`. La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.

Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos del elemento aspNetCore

| Atributo | Descripción | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadena opcional.</p><p>Argumentos para el archivo ejecutable especificado en **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Atributo Boolean opcional.</p><p>Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo Boolean opcional.</p><p>Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud. Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</p> | `true` |
| `hostingModel` | <p>Atributo de cadena opcional.</p><p>Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Atributo integer opcional.</p><p>Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</p><p>&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</p><p>No se recomienda establecer `processesPerApplication`. Este atributo se quitará en futuras versiones.</p> | Valor predeterminado: `1`<br>Mínimo: `1`<br>Máximo: `100`&dagger; |
| `processPath` | <p>Atributo de cadena necesario.</p><p>Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP. No se admiten rutas de acceso relativas. Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</p> | |
| `rapidFailsPerMinute` | <p>Atributo integer opcional.</p><p>Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto. Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</p><p>No admitido con el hospedaje en proceso.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `100` |
| `requestTimeout` | <p>Atributo timespan opcional.</p><p>Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</p><p>En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</p><p>No se aplica al hospedaje en proceso. En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</p><p>Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59. El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</p> | Valor predeterminado: `00:02:00`<br>Mínimo: `00:00:00`<br>Máximo: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `600` |
| `startupTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto. Si se supera este límite de tiempo, el módulo termina el proceso.</p><p>Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración **rapidFailsPerMinute**.</p><p>Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</p><p>Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</p> | Valor predeterminado: `120`<br>Mínimo: `0`<br>Máximo: `3600` |
| `stdoutLogEnabled` | <p>Atributo Boolean opcional.</p><p>Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadena opcional.</p><p>Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**. Las rutas de acceso relativas son relativas a la raíz del sitio. Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas. Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso. Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**. Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Configuración de las variables de entorno

Se pueden especificar variables de entorno para el proceso en el atributo `processPath`. Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`. Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.

En el ejemplo siguiente se establecen dos variables de entorno. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`. Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación. `CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto. Este método establece el entorno en *web.config* cuando se publica el proyecto:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes. Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.

Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*. Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.

Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta. Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.

## <a name="start-up-error-page"></a>Página de errores de inicio

Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.

Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.

Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.

En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.

Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`. Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Creación y redireccionamiento de registros

El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`. Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`. El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).

Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso. Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.

Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.

No use el registro de stdout con fines de registro de aplicaciones general. Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo. El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado. Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.

Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB. Después del inicio, se descartarán los registros adicionales.

En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`. Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`. La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.

Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Registros de diagnóstico mejorados

El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados. Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

El módulo no crea automáticamente las carpetas de la ruta de acceso proporcionada al valor `<handlerSetting>` (*logs* en el ejemplo anterior), que deben existir previamente en la implementación. El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).

Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.

Niveles (en orden de menos a más detallado):

* ERROR
* WARNING
* INFO
* TRACE

Ubicaciones (se permiten varias ubicaciones):

* CONSOLE
* EVENTLOG
* ARCHIVO

También se puede proporcionar la configuración de controlador a través de variables de entorno:

* `ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración. (El valor predeterminado es *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.

> [!WARNING]
> **No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema. El tamaño del registro no es limitado. Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.

Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>La configuración de proxy usa el protocolo HTTP y un token de emparejamiento

*Solo se aplica al hospedaje fuera de proceso.*

El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP. No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.

Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente. El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`). El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy. El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno. Si los valores del token no coinciden, la solicitud se registra y se rechaza. No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor. Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>El módulo ASP.NET Core con una configuración compartida de IIS

El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**. Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.

Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:

1. Deshabilite la configuración compartida de IIS.
1. Ejecute el instalador.
1. Exporte el archivo *applicationHost.config* actualizado al recurso compartido.
1. Vuelva a habilitar la configuración compartida de IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versión del módulo y registros del instalador de la agrupación de hospedaje

Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:

1. En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.
1. Busque el archivo *aspnetcore.dll*.
1. Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.
1. Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.

Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Ubicaciones del módulo, el esquema y el archivo de configuración

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Configuración

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para reenviar solicitudes web a aplicaciones ASP.NET Core de back-end.

Versiones de Windows compatibles:

* Windows 7 o posterior
* Windows Server 2008 R2 o posterior

El módulo funciona únicamente con Kestrel. El módulo no es compatible con [HTTP.sys](xref:fundamentals/servers/httpsys).

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo también se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud, y reinicia la aplicación si esta se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones ASP.NET 4.x que se ejecutan en el proceso en IIS y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación:

![Módulo ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS). El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.

Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos. Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.

El módulo ASP.NET Core también puede:

* Establecer variables de entorno para un proceso de trabajo.
* Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.
* Reenviar tokens de autenticación de Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Cómo instalar y usar el módulo ASP.NET Core

Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuración con web.config

El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.

El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`. La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.

Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos del elemento aspNetCore

| Atributo | Descripción | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadena opcional.</p><p>Argumentos para el archivo ejecutable especificado en **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Atributo Boolean opcional.</p><p>Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo Boolean opcional.</p><p>Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud. Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</p> | `true` |
| `processesPerApplication` | <p>Atributo integer opcional.</p><p>Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</p><p>No se recomienda establecer `processesPerApplication`. Este atributo se quitará en futuras versiones.</p> | Valor predeterminado: `1`<br>Mínimo: `1`<br>Máximo: `100` |
| `processPath` | <p>Atributo de cadena necesario.</p><p>Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP. No se admiten rutas de acceso relativas. Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</p> | |
| `rapidFailsPerMinute` | <p>Atributo integer opcional.</p><p>Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto. Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `100` |
| `requestTimeout` | <p>Atributo timespan opcional.</p><p>Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</p><p>En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</p> | Valor predeterminado: `00:02:00`<br>Mínimo: `00:00:00`<br>Máximo: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `600` |
| `startupTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto. Si se supera este límite de tiempo, el módulo termina el proceso. El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</p><p>Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</p> | Valor predeterminado: `120`<br>Mínimo: `0`<br>Máximo: `3600` |
| `stdoutLogEnabled` | <p>Atributo Boolean opcional.</p><p>Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadena opcional.</p><p>Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**. Las rutas de acceso relativas son relativas a la raíz del sitio. Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas. Las carpetas que se proporcionan en la ruta de acceso deben estar en orden para que el módulo cree el archivo de registro. Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**. Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Configuración de las variables de entorno

Se pueden especificar variables de entorno para el proceso en el atributo `processPath`. Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.

> [!WARNING]
> Las variables de entorno establecidas en esta sección entran en conflicto con las variables de entorno del sistema que tienen el mismo nombre. Si se establece una variable de entorno en el archivo *web.config* y en el nivel del sistema en Windows, el valor del archivo *web.config* se anexa al valor de la variable de entorno del sistema (por ejemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), que impide que se inicie la aplicación.

En el ejemplo siguiente se establecen dos variables de entorno. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`. Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación. `CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes. Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.

Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*. Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.

## <a name="start-up-error-page"></a>Página de errores de inicio

Si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparece una página de código de estado *502.5 - Error de proceso*. Para suprimir esta página y volver a la página de código de estado 502 de IIS predeterminada, use el atributo `disableStartUpErrorPage`. Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![Página de códigos de estado 502.5 Error de proceso](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Creación y redireccionamiento de registros

El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`. Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`. El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).

Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso. Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.

Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.

No use el registro de stdout con fines de registro de aplicaciones general. Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo. El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado. Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.

En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`. Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`. La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.

Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.

Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>La configuración de proxy usa el protocolo HTTP y un token de emparejamiento

El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP. No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.

Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente. El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`). El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy. El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno. Si los valores del token no coinciden, la solicitud se registra y se rechaza. No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor. Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>El módulo ASP.NET Core con una configuración compartida de IIS

El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**. Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.

Al usar una configuración compartida de IIS, siga estos pasos:

1. Deshabilite la configuración compartida de IIS.
1. Ejecute el instalador.
1. Exporte el archivo *applicationHost.config* actualizado al recurso compartido.
1. Vuelva a habilitar la configuración compartida de IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versión del módulo y registros del instalador de la agrupación de hospedaje

Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:

1. En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.
1. Busque el archivo *aspnetcore.dll*.
1. Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.
1. Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.

Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Ubicaciones del módulo, el esquema y el archivo de configuración

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Schema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Configuración

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [Origen de referencia del módulo ASP.NET Core (rama maestra)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): use la lista desplegable **Rama** para seleccionar una versión específica (por ejemplo, `release/3.1`).
* <xref:host-and-deploy/iis/modules>
