---
title: Archivo web.config
author: rick-anderson
description: Descubra lo que se encuentra dentro del archivo web.config y cómo configurar diferentes opciones del módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057289"
---
# <a name="webconfig-file"></a>Archivo `web.config`

`web.config` es un archivo que IIS y el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) leen para configurar una aplicación hospedada en IIS.

## <a name="webconfig-file-location"></a>Ubicación del archivo `web.config`

Para configurar el [módulo ASP.NET](xref:host-and-deploy/aspnet-core-module) correctamente, el archivo `web.config` debe estar presente en la ruta [raíz de contenido](xref:fundamentals/index#content-root) (normalmente la ruta de acceso base de la aplicación) de la aplicación implementada. Se trata de la misma ubicación que la ruta de acceso física del sitio web proporcionada a IIS. El archivo `web.config` debe estar en la raíz de la aplicación para habilitar la publicación de varias aplicaciones mediante Web Deploy.

Los archivos confidenciales están en la ruta de acceso física de la aplicación, como `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (comentarios de documentación XML), y `{ASSEMBLY}.deps.json`, donde el marcador de posición `{ASSEMBLY}` es el nombre del ensamblado. Si el archivo `web.config` está presente y el sitio se inicia normalmente, IIS no facilita estos archivos confidenciales, en el caso de que se soliciten. Si el archivo `web.config` no está presente, se le asignó un nombre incorrecto o no se puede configurar el sitio para un inicio normal, IIS puede servir archivos confidenciales públicamente.

**El archivo `web.config` debe estar presente en la implementación en todo momento, se le debe asignar un nombre correcto y debe ser capaz de configurar el sitio para el inicio normal. Nunca quite el archivo `web.config` de una implementación de producción.**

Si el proyecto no incluye un archivo `web.config`, el archivo se crea con los elementos `processPath` y `arguments` correctos para configurar el módulo ASP.NET Core y se mueve a la [salida publicada](xref:host-and-deploy/directory-structure).

Si el proyecto incluye un archivo `web.config`, el archivo se transforma con los elementos `processPath` y `arguments` correctos para configurar el módulo ASP.NET Core y se mueve a la salida publicada. La transformación no modifica los valores de configuración de IIS del archivo.

El archivo `web.config` puede proporcionar valores de configuración de IIS adicionales que controlan los módulos activos de IIS. Para información sobre los módulos de IIS que son capaces de procesar las solicitudes con aplicaciones ASP.NET Core, vea el tema [Módulos IIS](xref:host-and-deploy/iis/modules).

La creación, transformación y publicación del archivo `web.config` se controla por medio de un destino de MSBuild (`_TransformWebConfig`) cuando el proyecto se publica. Este destino está incluido entre los destinos del SDK web (`Microsoft.NET.Sdk.Web`). El SDK se establece al inicio del archivo del proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Para evitar que el SDK web transforme el archivo `web.config`, use la propiedad `<IsTransformWebConfigDisabled>` en el archivo del proyecto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Al deshabilitar el SDK web para la transformación del archivo, el desarrollador debe establecer el elemento `processPath` y los `arguments` manualmente. Para más información, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Configuración del módulo de ASP.NET Core con `web.config`

El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo `web.config` del sitio.

El siguiente archivo `web.config` se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:

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

El siguiente archivo `web.config` se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).

Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`. La ruta de acceso guarda los registros de stdout en la carpeta `LogFiles`, que es una ubicación que el servicio crea automáticamente.

Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos del elemento `aspNetCore`

| Atributo | Descripción | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadena opcional.</p><p>Argumentos para el archivo ejecutable especificado en `processPath`.</p> | |
| `disableStartUpErrorPage` | <p>Atributo Boolean opcional.</p><p>Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en `web.config`.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo Boolean opcional.</p><p>Si es "true", el token se reenvía al proceso secundario que escucha en `%ASPNETCORE_PORT%` como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud. Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</p> | `true` |
| `hostingModel` | <p>Atributo de cadena opcional.</p><p>Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Atributo integer opcional.</p><p>Especifica el número de instancias del proceso especificado en el valor `processPath` que pueden rotarse por aplicación.</p><p>&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</p><p>No se recomienda establecer `processesPerApplication`. Este atributo se quitará en futuras versiones.</p> | Valor predeterminado: `1`<br>Mínimo: `1`<br>Máximo: `100`&dagger; |
| `processPath` | <p>Atributo de cadena necesario.</p><p>Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP. No se admiten rutas de acceso relativas. Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</p> | |
| `rapidFailsPerMinute` | <p>Atributo integer opcional.</p><p>Especifica el número de veces que el proceso indicado en `processPath` puede bloquearse por minuto. Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</p><p>No admitido con el hospedaje en proceso.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `100` |
| `requestTimeout` | <p>Atributo timespan opcional.</p><p>Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</p><p>En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</p><p>No se aplica al hospedaje en proceso. En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</p><p>Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59. El uso de `60` en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</p> | Valor predeterminado: `00:02:00`<br>Mínimo: `00:00:00`<br>Máximo: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo `app_offline.htm`.</p> | Valor predeterminado: `10`<br>Mínimo: `0`<br>Máximo: `600` |
| `startupTimeLimit` | <p>Atributo integer opcional.</p><p>Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto. Si se supera este límite de tiempo, el módulo termina el proceso.</p><p>Al hospedar *en proceso* : El proceso **no** se reinicia y **no** usa la configuración `rapidFailsPerMinute`.</p><p>Al hospedar *fuera del proceso* : El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar `rapidFailsPerMinute` un número de veces en el último minuto acumulado.</p><p>Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</p> | Valor predeterminado: `120`<br>Mínimo: `0`<br>Máximo: `3600` |
| `stdoutLogEnabled` | <p>Atributo Boolean opcional.</p><p>Si es true, `stdout` y `stderr` en el proceso especificado en `processPath` se redirigen al archivo especificado en `stdoutLogFile`.</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadena opcional.</p><p>Especifica la ruta de acceso relativa o absoluta para la que se registran `stdout` y `stderr` desde el proceso especificado en `processPath`. Las rutas de acceso relativas son relativas a la raíz del sitio. Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas. Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso. Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile`. Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como `stdout_20180205194132_1934.log` en la carpeta `logs`, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Establecimiento de las variables de entorno

Se pueden especificar variables de entorno para el proceso en el atributo `processPath`. Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`. Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.

En el ejemplo siguiente se establecen dos variables de entorno en `web.config`. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`. Un desarrollador puede establecer temporalmente este valor en el archivo `web.config` con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación. `CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.

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
> Una alternativa a establecer directamente el entorno en `web.config` consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto. Este método establece el entorno en `web.config` cuando se publica el proyecto:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Configuración de IIS con `web.config`

En escenarios de IIS que son funcionales para aplicaciones ASP.NET Core con el módulo ASP.NET Core, la configuración de IIS está influenciada por la sección `<system.webServer>` de `web.config`. Por ejemplo, la configuración de IIS es funcional para la compresión dinámica. Si IIS está configurado en el nivel de servidor para usar compresión dinámica, el elemento `<urlCompression>` del archivo `web.config` de la aplicación puede deshabilitarlo para una aplicación ASP.NET Core.

Para obtener más información, vea los temas siguientes:

* [Referencia de configuración para `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Para establecer variables de entorno para aplicaciones individuales que se ejecutan en grupos de aplicaciones aislados (compatible con IIS 10.0 o posterior), vea la sección *Comando `AppCmd.exe`* del tema [Variables de entorno `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) de la documentación de referencia de IIS.

## <a name="configuration-sections-of-webconfig"></a>Secciones de configuración de `web.config`

Las aplicaciones ASP.NET Core no usan las secciones de configuración de aplicaciones ASP.NET 4.x en `web.config` para la configuración:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Las aplicaciones de ASP.NET Core se configuran mediante otros proveedores de configuración. Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Transformación de web.config

Si necesita transformar `web.config` al realizar la publicación, vea <xref:host-and-deploy/iis/transform-webconfig>. Es posible que necesite transformar `web.config` al realizar la publicación para establecer variables de entorno basadas en la configuración, el perfil o el entorno.

## <a name="additional-resources"></a>Recursos adicionales

* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
