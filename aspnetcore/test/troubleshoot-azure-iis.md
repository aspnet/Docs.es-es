---
title: Solución de problemas de ASP.NET Core en Azure App Service e IIS
author: rick-anderson
description: Aprenda a diagnosticar problemas con las implementaciones de Azure App Service e Internet Information Services (IIS) de aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: a8bb659a72e8370c31cc9c213689399e74f11b5c
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564058"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Solución de problemas de ASP.NET Core en Azure App Service e IIS

Por [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

En este artículo se proporciona información sobre los errores comunes de inicio de la aplicación e instrucciones sobre cómo diagnosticar errores cuando una aplicación se implementa en Azure App Service o IIS:

[Errores de inicio de aplicación](#app-startup-errors)  
Se explican escenarios comunes de los códigos de estado HTTP de inicio.

[Solución de problemas en Azure App Service](#troubleshoot-on-azure-app-service)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en Azure App Service.

[Solución de problemas de IIS](#troubleshoot-on-iis)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en IIS o que se ejecutan en IIS Express de forma local. La guía se aplica a las implementaciones de Windows Server y escritorio de Windows.

[Borrado de memorias caché de paquetes](#clear-package-caches)  
Se explica qué hacer cuando hay paquetes incoherentes que interrumpen una aplicación al realizar actualizaciones importantes o cambiar las versiones de los paquetes.

[Recursos adicionales](#additional-resources)  
Se incluyen temas adicionales de solución de problemas.

## <a name="app-startup-errors"></a>Errores de inicio de aplicación

En Visual Studio, un proyecto de ASP.NET Core toma como predeterminado el hospedaje de [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante la depuración. El código de estado *502.5 - Error de proceso* o *500.30 - Error de inicio* que se produce al efectuar la depuración localmente se puede diagnosticar si se sigue el consejo de este tema.

### <a name="40314-forbidden"></a>403.14 Prohibido

No se puede iniciar la aplicación. Se registra el siguiente error:

```
The Web server is configured to not list the contents of this directory.
```

El error se suele deber a una implementación interrumpida en el sistema de hospedaje, que incluye cualquiera de los siguientes escenarios:

* La aplicación se implementa en la carpeta incorrecta en el sistema de hospedaje.
* El proceso de implementación no pudo trasladar todos los archivos y las carpetas de la aplicación a la carpeta de implementación en el sistema de hospedaje.
* Falta el archivo *web.config* en la implementación o el contenido del archivo *web.config* no tiene un formato correcto.

Lleve a cabo los siguiente pasos:

1. Elimine todos los archivos y las carpetas de la carpeta de implementación en el sistema de hospedaje.
1. Vuelva a implementar el contenido de la carpeta *publish* de la aplicación en el sistema de hospedaje con un método normal de implementación, como Visual Studio, PowerShell o la implementación manual:
   * Confirme que el archivo *web.config* está en la implementación y que su contenido es correcto.
   * Cuando hospede contenido en Azure App Service, confirme que la aplicación se implementa en la carpeta `D:\home\site\wwwroot`.
   * Si IIS hospeda la aplicación, confirme que la aplicación se implementa en la **ruta de acceso física** de IIS que se muestra en la **configuración básica** del **administrador de IIS**.
1. Confirme que todos los archivos y las carpetas de la aplicación se han implementado; para ello, compare la implementación en el sistema de hospedaje con el contenido de la carpeta *publish* del proyecto.

Para obtener más información sobre el diseño de una aplicación ASP.NET Core publicada, consulte <xref:host-and-deploy/directory-structure>. Para obtener más información sobre el archivo *web.config*, vea <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 Error interno del servidor

La aplicación se inicia, pero un error impide que el servidor complete la solicitud.

Este error se produce dentro del código de la aplicación durante el inicio o mientras se crea una respuesta. La respuesta no puede contener nada o puede aparecer como *500 Error interno del servidor* en el explorador. El registro de eventos de la aplicación normalmente indica que la aplicación se ha iniciado normalmente. Desde la perspectiva del servidor, eso es correcto. La aplicación se inició, pero no puede generar una respuesta válida. Ejecute la aplicación en un símbolo del sistema en el servidor o habilite el registro de stdout del módulo ASP.NET Core para solucionar el problema.

### <a name="5000-in-process-handler-load-failure"></a>500.0 Error de carga del controlador en proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

Error desconocido al cargar los componentes del [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Realice una de las siguientes acciones:

* Póngase en contacto con el [equipo de soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (seleccione **Herramientas de desarrollo** y, después, **ASP.NET Core**).
* Formule una pregunta en Stack Overflow.
* Registre un problema en nuestro [repositorio de GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 Error de inicio en proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) intenta iniciar el proceso CLR de .NET Core, pero no lo consigue. La causa del error de inicio del proceso se suele determinar a partir de las entradas del registro de eventos de la aplicación y del registro de stdout del módulo ASP.NET Core.

Condiciones de error habituales:

* La aplicación está mal configurada porque tiene como destino una versión del marco compartido de ASP.NET Core que no está presente. Compruebe qué versiones del marco compartido de ASP.NET Core están instaladas en el equipo de destino.
* Al usar Azure Key Vault, faltan permisos de la instancia de Key Vault. Compruebe las directivas de acceso de la instancia de Key Vault de destino para asegurarse de que se concedan los permisos correctos.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 ANMC no pudo encontrar las dependencias nativas

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) intenta iniciar el proceso del entorno de ejecución de .NET Core, pero no lo consigue. Este error de inicio suele deberse a que el entorno de ejecución de `Microsoft.NETCore.App` o `Microsoft.AspNetCore.App` no está instalado. Si la aplicación se implementa para ASP.NET Core 3.0 y esa versión no existe en el equipo, se produce este error. Este es un mensaje de error de ejemplo:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

El mensaje de error enumera todas las versiones instaladas de .NET Core y la versión solicitada por la aplicación. Para corregir este error, hay dos posibilidades:

* Instalar la versión adecuada de .NET Core en la máquina.
* Cambiar el destino de la aplicación a una versión de .NET Core que exista en la máquina.
* Publique la aplicación como una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd).

Cuando se ejecuta en desarrollo (la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece en `Development`), el error específico se escribe en la respuesta HTTP. La causa de un error de inicio de proceso también se encuentra en el registro de eventos de la aplicación.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM No se pudo cargar el archivo .dll

El proceso de trabajo no funciona. La aplicación no se inicia.

La causa más común de este error es que la aplicación se haya publicado para una arquitectura de procesador no compatible. Si el proceso de trabajo se ejecuta como una aplicación de 32 bits y la aplicación se diseñó para 64 bits, se produce este error.

Para corregir este error, hay dos posibilidades:

* Volver a publicar la aplicación para la misma arquitectura de procesador que el proceso de trabajo.
* Publicar la aplicación como una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 Error de carga del controlador de solicitud

El proceso de trabajo no funciona. La aplicación no se inicia.

La aplicación no hizo referencia al marco `Microsoft.AspNetCore.App`. Solo las aplicaciones diseñadas para el marco `Microsoft.AspNetCore.App` pueden hospedarse en el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Para corregir este error, confirme que la aplicación tiene como destino el marco `Microsoft.AspNetCore.App`. Compruebe en `.runtimeconfig.json` cuál es el marco de destino de la aplicación.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 ANCM Modelos de hospedaje mixto no admitidos

El proceso de trabajo no puede ejecutar a la vez una aplicación en proceso y una aplicación fuera de proceso.

Para corregir este error, ejecute las aplicaciones en grupos de aplicaciones de IIS independientes.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM Varias aplicaciones en proceso en el mismo proceso

El proceso de trabajo no puede ejecutar varias aplicaciones en proceso en el mismo proceso.

Para corregir este error, ejecute las aplicaciones en grupos de aplicaciones de IIS independientes.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 Error de carga del controlador fuera de proceso

El controlador de solicitudes de fuera de proceso, *aspnetcorev2_outofprocess.dll*, no está junto al archivo *aspnetcorev2.dll*. Esto indica una instalación dañada del [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Para corregir este error, repare la instalación del [conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (para IIS) o Visual Studio (para IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 ANCM no se pudo iniciar en el límite de tiempo de inicio

ANCM no se pudo iniciar dentro del límite de tiempo de inicio proporcionado. De forma predeterminada, el tiempo de espera es de 120 segundos.

Este error puede producirse cuando se inicia un gran número de aplicaciones en el mismo equipo. Busque picos de uso de CPU o memoria en el servidor durante el inicio. Es posible que deba escalonar el proceso de inicio de varias aplicaciones.

### <a name="50038-ancm-application-dll-not-found"></a>500.38 No se encontró el archivo DLL de la aplicación ANCM

ANCM no pudo encontrar el archivo DLL de la aplicación, que debe estar junto al archivo ejecutable.

Este error se produce al hospedar una aplicación empaquetada como [ejecutable de un solo archivo](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) mediante el modelo de hospedaje In-Process. El modelo In-Process requiere que ANCM cargue la aplicación .NET Core en el proceso de IIS existente. Este escenario no es compatible con el modelo de implementación de un archivo único. Use **uno** de los enfoques siguientes en el archivo del proyecto de la aplicación para corregir este error:

1. Deshabilite la publicación de un archivo único; para ello, establezca la propiedad `PublishSingleFile` de MSBuild en `false`.
1. Cambie al modelo de hospedaje fuera de proceso estableciendo la propiedad `AspNetCoreHostingModel` de MSBuild en `OutOfProcess`.

### <a name="5025-process-failure"></a>502.5 Error de proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) intenta iniciar el proceso de trabajo, pero no lo consigue. La causa del error de inicio del proceso se suele determinar a partir de las entradas del registro de eventos de la aplicación y del registro de stdout del módulo ASP.NET Core.

Una condición de error habitual es que la aplicación esté mal configurada porque tiene como destino una versión del marco compartido de ASP.NET Core que no está presente. Compruebe qué versiones del marco compartido de ASP.NET Core están instaladas en el equipo de destino. El *marco de trabajo compartido* es el conjunto de ensamblados (archivos *.dll*) que se instalan en la máquina, y se hace referencia a ellos mediante un metapaquete como `Microsoft.AspNetCore.App`. La referencia del metapaquete puede especificar una versión mínima necesaria. Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La página de error *502.5 Error de proceso* se devuelve cuando el proceso de trabajo no se puede iniciar debido a un error de configuración de la aplicación o del hospedaje:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>No se pudo iniciar la aplicación (código de error "0x800700c1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Error al iniciar la aplicación porque el ensamblado de la aplicación ( *.dll*) no se ha podido cargar.

Este error se produce cuando hay un error de coincidencia del valor de bits entre la aplicación publicada y el proceso w3wp/iisexpress.

Confirme que la opción de 32 bits del grupo de aplicaciones sea correcta:

1. Seleccione el grupo de aplicaciones en **Grupos de aplicaciones** del Administrador de IIS.
1. Seleccione **Configuración avanzada** en **Modificar grupo de aplicaciones**, en el panel **Acciones**.
1. Establezca **Habilitar aplicaciones de 32 bits**:
   * Si implementa una aplicación de 32 bits (x86), establezca el valor en `True`.
   * Si implementa una aplicación de 64 bits (x64), establezca el valor en `False`.

Confirme que no hay ningún conflicto entre una propiedad `<Platform>` de MSBuild en el archivo del proyecto y el valor de bits publicado de la aplicación.

### <a name="connection-reset"></a>Restablecimiento de la conexión

Si se produce un error después de que se envían los encabezados, el servidor no tiene tiempo para enviar un mensaje **500 Error interno del servidor** cuando se produce un error. Esto suele ocurrir cuando se produce un error durante la serialización de objetos complejos en una respuesta. Este tipo de error aparece como un error de *restablecimiento de la conexión* en el cliente. El [Registro de aplicaciones](xref:fundamentals/logging/index) puede ayudar a solucionar estos tipos de errores.

### <a name="default-startup-limits"></a>Límites de inicio predeterminados

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) está configurado con un valor predeterminado de *startupTimeLimit* de 120 segundos. Cuando se deja en el valor predeterminado, una aplicación puede tardar hasta dos minutos en iniciarse antes de que el módulo registre un error de proceso. Para información sobre la configuración del módulo, consulte [Atributos del elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Solución de problemas en Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Registro de eventos de la aplicación (Azure App Service)

Para acceder al registro de eventos de la aplicación, use la hoja **Diagnose and solve problems** (Diagnosticar y solucionar problemas) de Azure Portal:

1. En Azure Portal, abra la aplicación en **App Services**.
1. Seleccione **Diagnosticar y solucionar problemas**.
1. Seleccione el título **Herramientas de diagnóstico**.
1. En **Herramientas de soporte técnico**, seleccione el botón **Eventos de la aplicación**.
1. Examine el error más reciente que hayan proporcionado las entradas *IIS AspNetCoreModule* o *IIS AspNetCoreModule V2* en la columna **Origen**.

Una alternativa al uso de la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) es examinar el archivo de registro de eventos de la aplicación directamente mediante [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra la carpeta **LogFiles**.
1. Seleccione el icono de lápiz junto al archivo *eventlog.xml*.
1. Examine el registro. Desplácese al final del registro para ver los eventos más recientes.

### <a name="run-the-app-in-the-kudu-console"></a>Ejecución de la aplicación en la consola de Kudu

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. Puede ejecutar la aplicación en la consola de ejecución remota de [Kudu](https://github.com/projectkudu/kudu/wiki) para detectar el error:

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Prueba de una aplicación de 32 bits (x86)

**Versión actual**

1. `cd d:\home\site\wwwroot`
1. Ejecute la aplicación:
   * Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Prueba de una aplicación de 64 bits (x64)

**Versión actual**

* Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):
  1. `cd D:\Program Files\dotnet`
  1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Ejecute la aplicación: `{ASSEMBLY NAME}.exe`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Registro de stdout del módulo ASP.NET Core (Azure App Service)

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados. Use únicamente el registro de stdout para solucionar problemas de inicio de la aplicación.
>
> Para el registro general en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

El registro stdout del módulo ASP.NET Core con frecuencia registra mensajes de error útiles que no se encuentran en el registro de eventos de la aplicación. Para habilitar y ver los registros de stdout:

1. En Azure Portal, vaya a la aplicación web.
1. En la hoja **App Service**, escriba **kudu** en el cuadro de búsqueda.
1. Seleccione **Herramientas avanzadas** > **Ir**.
1. Seleccione **Consola de depuración > CMD**.
1. Vaya a *site/wwwroot*.
1. Seleccione el icono de lápiz para editar el archivo *web.config*.
1. En el elemento `<aspNetCore />`, establezca `stdoutLogEnabled="true"` y seleccione **Guardar**.

Establezca `stdoutLogEnabled="false"` para deshabilitar el registro de stdout una vez que haya solucionado los problemas.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

<a name="enhanced-diagnostic-logs"></a>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Registro de depuración del módulo ASP.NET Core (Azure App Service)

El registro de depuración del módulo de ASP.NET Core ofrece un registro adicional, más profundo, del módulo ASP.NET Core. Para habilitar y ver los registros de stdout:

1. Para habilitar el registro de diagnóstico mejorado, realice cualquiera de las acciones siguientes:
   * Siga las instrucciones de [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para configurar la aplicación para un registro de diagnóstico mejorado. Vuelva a implementar la aplicación.
   * Agregue la `<handlerSettings>` que se muestra en [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) al archivo *web.config* de la aplicación activa mediante la consola de Kudu:
     1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
     1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
     1. Abra las carpetas para la ruta de acceso **site** > **wwwroot**. Seleccione el icono de lápiz para editar el archivo *web.config*. Agregue la sección `<handlerSettings>` como se muestra en [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Seleccione el botón **Guardar**.
1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra las carpetas para la ruta de acceso **site** > **wwwroot**. Si no ha proporcionado una ruta de acceso para el archivo *aspnetcore debug.log*, el archivo aparece en la lista. Si ha proporcionado una ruta de acceso, vaya a la ubicación del archivo de registro.
1. Abra el archivo de registro con el botón de lápiz situado junto al nombre del archivo.

Deshabilite el registro de depuración una vez haya solucionado los problemas:

Para deshabilitar el registro de depuración mejorado, realice cualquiera de las acciones siguientes:

* Quite localmente la `<handlerSettings>` del archivo *web.config* y vuelva a implementar la aplicación.
* Use la consola de Kudu para editar el archivo *web.config* y quite la sección `<handlerSettings>`. Guarde el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Si no deshabilita el registro de depuración, es posible que se produzcan errores en la aplicación o el servidor. No hay ningún límite para el tamaño del archivo de registro. Use únicamente el registro de depuración para solucionar problemas de inicio de la aplicación.
>
> Para el registro general en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplicación lenta o bloqueada (Azure App Service)

Cuando una aplicación responde con lentitud o se bloquea en una solicitud, consulte los artículos siguientes:

* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) (Uso de la extensión de sitio de diagnóstico de bloqueo para capturar el volcado de memoria para problemas de excepciones intermitentes o problemas de rendimiento en Azure Web Apps)

### <a name="monitoring-blades"></a>Hojas de supervisión

Las hojas de supervisión proporcionan una alternativa a la experiencia de solución de problemas de los métodos descritos anteriormente en el tema. Estas hojas se pueden usar para diagnosticar errores de la serie 500.

Confirme que están instaladas las extensiones de ASP.NET Core. Si no lo están, instálelas manualmente:

1. En la sección de la hoja **HERRAMIENTAS DE DESARROLLO**, seleccione la hoja **Extensiones**.
1. Aparecerán en la lista las **extensiones de ASP.NET Core**.
1. Si las extensiones no están instaladas, seleccione el botón **Add** (Agregar).
1. Elija las **extensiones de ASP.NET Core** de la lista.
1. Seleccione **Aceptar** para aceptar los términos legales.
1. Seleccione **Aceptar** en la hoja **Agregar extensión**.
1. Un mensaje emergente informativo indica si las extensiones se han instalado correctamente.

Si el registro de stdout no está habilitado, siga estos pasos:

1. En Azure Portal, seleccione la hoja **Herramientas avanzadas** en el área **HERRAMIENTAS DE DESARROLLO**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra las carpetas a la ruta de acceso **sitio** > **wwwroot** y desplácese hacia abajo para mostrar el archivo *web.config* en la parte inferior de la lista.
1. Haga clic en el icono de lápiz junto al archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** a: `\\?\%home%\LogFiles\stdout`.
1. Seleccione **Save** (Guardar) para guardar el archivo *web.config* actualizado.

Continúe para activar el registro de diagnóstico:

1. En Azure Portal, seleccione la hoja **Registros de diagnóstico**.
1. Seleccione el conmutador **Activado** en **Registro de la aplicación (sistema de archivos)** y **Mensajes de error detallados**. Seleccione el botón **Guardar** en la parte superior de la hoja.
1. Para incluir el seguimiento de solicitudes con error, también conocido como almacenamiento en búfer de eventos de solicitudes con error (FREB), seleccione el conmutador **Activado** en **Seguimiento de solicitudes con error**.
1. Seleccione la hoja **Secuencia de registro**, que aparece inmediatamente bajo la hoja **Registros de diagnóstico** en el portal.
1. Realice una solicitud a la aplicación.
1. Dentro de los datos de la secuencia de registro, se indica la causa del error.

No olvide deshabilitar el registro de stdout cuando finalice la solución de problemas.

Para ver los registros de seguimiento de solicitudes con error (registros FREB):

1. Vaya a la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) de Azure Portal.
1. Seleccione **Failed Request Tracing Logs** (Registros de seguimiento de solicitudes con error) en el área **SUPPORT TOOLS** (HERRAMIENTAS DE SOPORTE TÉCNICO) de la barra lateral.

Para más información, consulte la [sección sobre los seguimientos de solicitudes con error del tema Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) y el artículo [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure: ¿Cómo se activa el seguimiento de solicitudes con error?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing).

Para más información, consulte [Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Solución de problemas de IIS

### <a name="application-event-log-iis"></a>Registro de eventos de la aplicación (IIS)

Acceda al registro de eventos de la aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error. Los errores tienen un valor de *Módulo AspNetCore de IIS* o *Módulo AspNetCore de IIS Express* en la columna *Origen*.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.

#### <a name="framework-dependent-deployment"></a>Implementación dependiente de marco de trabajo

Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute la aplicación mediante la ejecución del ensamblado de la aplicación con *dotnet.exe*. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `dotnet .\<assembly_name>.dll`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

#### <a name="self-contained-deployment"></a>Implementación autocontenida

Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute el archivo ejecutable de la aplicación. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `<assembly_name>.exe`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

### <a name="aspnet-core-module-stdout-log-iis"></a>Registro de stdout del módulo ASP.NET Core (IIS)

Para habilitar y ver los registros de stdout:

1. Vaya a la carpeta de implementación del sitio en el sistema de hospedaje.
1. Si la carpeta *Logs* no existe, cree la carpeta. Para obtener instrucciones sobre cómo habilitar MSBuild para crear la carpeta *logs* automáticamente en la implementación, consulte el tema [Estructura de directorios](xref:host-and-deploy/directory-structure).
1. Edite el archivo *web.config*. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** para que apunte a la carpeta *logs* (por ejemplo, `.\logs\stdout`). `stdout` en la ruta de acceso es el prefijo del nombre del archivo de registro. Cuando se crea el registro, se agregan automáticamente una marca de tiempo, un identificador de proceso y una extensión de archivo. Cuando se usa `stdout` como prefijo para el nombre de archivo, un archivo de registro se llama normalmente *stdout_20180205184032_5412.log*.
1. Asegúrese de que la identidad del grupo de aplicaciones tiene permisos de escritura en la carpeta *logs*.
1. Guarde el archivo *web.config* actualizado.
1. Realice una solicitud a la aplicación.
1. Vaya a la carpeta *logs*. Busque y abra el registro más reciente de stdout.
1. Estudie el registro para ver los errores.

Deshabilite el registro de stdout una vez haya solucionado los problemas:

1. Edite el archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `false`.
1. Guarde el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Registro de depuración del módulo ASP.NET Core (IIS)

Agregue la siguiente configuración de controlador al archivo *web.config* de la aplicación para habilitar el registro de depuración del módulo ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Confirme que la ruta de acceso especificada para el registro exista y que la identidad del grupo de aplicaciones tenga permisos de escritura en la ubicación.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Habilitación de la página de excepciones para el desarrollador

La [variable de entorno `ASPNETCORE_ENVIRONMENT` se puede agregar a web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para ejecutar la aplicación en el entorno de desarrollo. Siempre y cuando el entorno no se invalide al inicio de la aplicación con `UseEnvironment` en el generador de host, la configuración de la variable de entorno permite que aparezca la [página de excepciones del desarrollador](xref:fundamentals/error-handling) cuando se ejecuta la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Solo se recomienda establecer la variable de entorno para `ASPNETCORE_ENVIRONMENT` cuando se use en servidores de ensayo o pruebas que no estén expuestos a Internet. Quite la variable de entorno del archivo *web.config* cuando termine de solucionar los problemas. Para información sobre la configuración de variables de entorno en *web.config*, consulte el [elemento secundario environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtención de datos de una aplicación

Si una aplicación es capaz de responder a solicitudes, obtenga solicitudes, conexiones y datos adicionales de la aplicación mediante el middleware en línea del terminal. Para obtener más información y un código de ejemplo, vea <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Aplicación lenta o bloqueada (IIS)

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`. El grupo de aplicaciones debe tener acceso de escritura a la carpeta.
1. Ejecute el [script EnableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentación de Azure

* [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sección sobre la depuración remota de las aplicaciones web del artículo Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Introducción a diagnósticos de Azure App Service](/azure/app-service/app-service-diagnostics)
* [Cómo: Supervisar aplicaciones en Azure App Service](/azure/app-service/web-sites-monitor)
* [Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Solucionar los errores HTTP de "502 Puerta de enlace no válida" y "503 Servicio no disponible" en las aplicaciones web de Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) (Espacio aislado de Azure Web App [limitaciones de ejecución del entono de tiempo de ejecución de App Service])
* [Azure Friday: experiencia de diagnóstico y solución de problemas de Azure App Service (vídeo de 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentación de Visual Studio

* [Depuración remota de ASP.NET Core en IIS en Azure en Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Depuración remota de ASP.NET Core en un equipo remoto de IIS en Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Información sobre cómo depurar con Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentación de Visual Studio Code

* [Depuración con Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

En este artículo se proporciona información sobre los errores comunes de inicio de la aplicación e instrucciones sobre cómo diagnosticar errores cuando una aplicación se implementa en Azure App Service o IIS:

[Errores de inicio de aplicación](#app-startup-errors)  
Se explican escenarios comunes de los códigos de estado HTTP de inicio.

[Solución de problemas en Azure App Service](#troubleshoot-on-azure-app-service)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en Azure App Service.

[Solución de problemas de IIS](#troubleshoot-on-iis)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en IIS o que se ejecutan en IIS Express de forma local. La guía se aplica a las implementaciones de Windows Server y escritorio de Windows.

[Borrado de memorias caché de paquetes](#clear-package-caches)  
Se explica qué hacer cuando hay paquetes incoherentes que interrumpen una aplicación al realizar actualizaciones importantes o cambiar las versiones de los paquetes.

[Recursos adicionales](#additional-resources)  
Se incluyen temas adicionales de solución de problemas.

## <a name="app-startup-errors"></a>Errores de inicio de aplicación

En Visual Studio, un proyecto de ASP.NET Core toma como predeterminado el hospedaje de [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante la depuración. El código de estado *502.5 - Error de proceso* o *500.30 - Error de inicio* que se produce al efectuar la depuración localmente se puede diagnosticar si se sigue el consejo de este tema.

### <a name="40314-forbidden"></a>403.14 Prohibido

No se puede iniciar la aplicación. Se registra el siguiente error:

```
The Web server is configured to not list the contents of this directory.
```

El error se suele deber a una implementación interrumpida en el sistema de hospedaje, que incluye cualquiera de los siguientes escenarios:

* La aplicación se implementa en la carpeta incorrecta en el sistema de hospedaje.
* El proceso de implementación no pudo trasladar todos los archivos y las carpetas de la aplicación a la carpeta de implementación en el sistema de hospedaje.
* Falta el archivo *web.config* en la implementación o el contenido del archivo *web.config* no tiene un formato correcto.

Lleve a cabo los siguiente pasos:

1. Elimine todos los archivos y las carpetas de la carpeta de implementación en el sistema de hospedaje.
1. Vuelva a implementar el contenido de la carpeta *publish* de la aplicación en el sistema de hospedaje con un método normal de implementación, como Visual Studio, PowerShell o la implementación manual:
   * Confirme que el archivo *web.config* está en la implementación y que su contenido es correcto.
   * Cuando hospede contenido en Azure App Service, confirme que la aplicación se implementa en la carpeta `D:\home\site\wwwroot`.
   * Si IIS hospeda la aplicación, confirme que la aplicación se implementa en la **ruta de acceso física** de IIS que se muestra en la **configuración básica** del **administrador de IIS**.
1. Confirme que todos los archivos y las carpetas de la aplicación se han implementado; para ello, compare la implementación en el sistema de hospedaje con el contenido de la carpeta *publish* del proyecto.

Para obtener más información sobre el diseño de una aplicación ASP.NET Core publicada, consulte <xref:host-and-deploy/directory-structure>. Para obtener más información sobre el archivo *web.config*, vea <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 Error interno del servidor

La aplicación se inicia, pero un error impide que el servidor complete la solicitud.

Este error se produce dentro del código de la aplicación durante el inicio o mientras se crea una respuesta. La respuesta no puede contener nada o puede aparecer como *500 Error interno del servidor* en el explorador. El registro de eventos de la aplicación normalmente indica que la aplicación se ha iniciado normalmente. Desde la perspectiva del servidor, eso es correcto. La aplicación se inició, pero no puede generar una respuesta válida. Ejecute la aplicación en un símbolo del sistema en el servidor o habilite el registro de stdout del módulo ASP.NET Core para solucionar el problema.

### <a name="5000-in-process-handler-load-failure"></a>500.0 Error de carga del controlador en proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) no logra encontrar el CLR de .NET Core ni el controlador de solicitudes en proceso (*aspnetcorev2_inprocess.dll*). Compruebe lo siguiente:

* Que la aplicación tiene como destino el paquete de NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) o el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
* Que la versión del marco compartido de ASP.NET Core que la aplicación tiene como destino esté instalada en el equipo de destino.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Error de carga del controlador fuera de proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) no logra encontrar el controlador de solicitudes de hospedaje fuera de proceso. Asegúrese de que el archivo *aspnetcorev2_outofprocess.dll* está presente en una subcarpeta junto a *aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 Error de proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) intenta iniciar el proceso de trabajo, pero no lo consigue. La causa del error de inicio del proceso se suele determinar a partir de las entradas del registro de eventos de la aplicación y del registro de stdout del módulo ASP.NET Core.

Una condición de error habitual es que la aplicación esté mal configurada porque tiene como destino una versión del marco compartido de ASP.NET Core que no está presente. Compruebe qué versiones del marco compartido de ASP.NET Core están instaladas en el equipo de destino. El *marco de trabajo compartido* es el conjunto de ensamblados (archivos *.dll*) que se instalan en la máquina, y se hace referencia a ellos mediante un metapaquete como `Microsoft.AspNetCore.App`. La referencia del metapaquete puede especificar una versión mínima necesaria. Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La página de error *502.5 Error de proceso* se devuelve cuando el proceso de trabajo no se puede iniciar debido a un error de configuración de la aplicación o del hospedaje:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>No se pudo iniciar la aplicación (código de error "0x800700c1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Error al iniciar la aplicación porque el ensamblado de la aplicación ( *.dll*) no se ha podido cargar.

Este error se produce cuando hay un error de coincidencia del valor de bits entre la aplicación publicada y el proceso w3wp/iisexpress.

Confirme que la opción de 32 bits del grupo de aplicaciones sea correcta:

1. Seleccione el grupo de aplicaciones en **Grupos de aplicaciones** del Administrador de IIS.
1. Seleccione **Configuración avanzada** en **Modificar grupo de aplicaciones**, en el panel **Acciones**.
1. Establezca **Habilitar aplicaciones de 32 bits**:
   * Si implementa una aplicación de 32 bits (x86), establezca el valor en `True`.
   * Si implementa una aplicación de 64 bits (x64), establezca el valor en `False`.

Confirme que no hay ningún conflicto entre una propiedad `<Platform>` de MSBuild en el archivo del proyecto y el valor de bits publicado de la aplicación.

### <a name="connection-reset"></a>Restablecimiento de la conexión

Si se produce un error después de que se envían los encabezados, el servidor no tiene tiempo para enviar un mensaje **500 Error interno del servidor** cuando se produce un error. Esto suele ocurrir cuando se produce un error durante la serialización de objetos complejos en una respuesta. Este tipo de error aparece como un error de *restablecimiento de la conexión* en el cliente. El [Registro de aplicaciones](xref:fundamentals/logging/index) puede ayudar a solucionar estos tipos de errores.

### <a name="default-startup-limits"></a>Límites de inicio predeterminados

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) está configurado con un valor predeterminado de *startupTimeLimit* de 120 segundos. Cuando se deja en el valor predeterminado, una aplicación puede tardar hasta dos minutos en iniciarse antes de que el módulo registre un error de proceso. Para información sobre la configuración del módulo, consulte [Atributos del elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Solución de problemas en Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Registro de eventos de la aplicación (Azure App Service)

Para acceder al registro de eventos de la aplicación, use la hoja **Diagnose and solve problems** (Diagnosticar y solucionar problemas) de Azure Portal:

1. En Azure Portal, abra la aplicación en **App Services**.
1. Seleccione **Diagnosticar y solucionar problemas**.
1. Seleccione el título **Herramientas de diagnóstico**.
1. En **Herramientas de soporte técnico**, seleccione el botón **Eventos de la aplicación**.
1. Examine el error más reciente que hayan proporcionado las entradas *IIS AspNetCoreModule* o *IIS AspNetCoreModule V2* en la columna **Origen**.

Una alternativa al uso de la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) es examinar el archivo de registro de eventos de la aplicación directamente mediante [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra la carpeta **LogFiles**.
1. Seleccione el icono de lápiz junto al archivo *eventlog.xml*.
1. Examine el registro. Desplácese al final del registro para ver los eventos más recientes.

### <a name="run-the-app-in-the-kudu-console"></a>Ejecución de la aplicación en la consola de Kudu

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. Puede ejecutar la aplicación en la consola de ejecución remota de [Kudu](https://github.com/projectkudu/kudu/wiki) para detectar el error:

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Prueba de una aplicación de 32 bits (x86)

**Versión actual**

1. `cd d:\home\site\wwwroot`
1. Ejecute la aplicación:
   * Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Prueba de una aplicación de 64 bits (x64)

**Versión actual**

* Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):
  1. `cd D:\Program Files\dotnet`
  1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Ejecute la aplicación: `{ASSEMBLY NAME}.exe`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Registro de stdout del módulo ASP.NET Core (Azure App Service)

El registro stdout del módulo ASP.NET Core con frecuencia registra mensajes de error útiles que no se encuentran en el registro de eventos de la aplicación. Para habilitar y ver los registros de stdout:

1. Vaya a la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) de Azure Portal.
1. En **Seleccione una categoría de problema**, seleccione el botón abajo **Aplicación web**.
1. En **Suggested Solutions** (Soluciones sugeridas) > **Enable Stdout Log Redirection** (Habilitar el redireccionamiento de registros de stdout), seleccione el botón para **abrir la consola de Kudu y editar web.config**.
1. En la **consola de diagnóstico** de Kudu, abra las carpetas para la ruta de acceso **site** > **wwwroot**. Desplácese hacia abajo para mostrar el archivo *web.config* en la parte inferior de la lista.
1. Haga clic en el icono de lápiz junto al archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** a: `\\?\%home%\LogFiles\stdout`.
1. Seleccione **Save** (Guardar) para guardar el archivo *web.config* actualizado.
1. Realice una solicitud a la aplicación.
1. Vuelva a Azure Portal. Seleccione la hoja **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Seleccione la carpeta **LogFiles**.
1. Inspeccione la columna **Modificado** y seleccione el icono de lápiz para editar el registro de stdout con la última fecha de modificación.
1. Cuando se abre el archivo de registro, se muestra el error.

Deshabilite el registro de stdout una vez haya solucionado los problemas:

1. En la **consola de diagnóstico** de Kudu, vuelva a la ruta de acceso **site** > **wwwroot** para mostrar el archivo *web.config*. Seleccione el icono de lápiz para abrir de nuevo el archivo **web.config**.
1. Establezca **stdoutLogEnabled** en `false`.
1. Seleccione **Save** (Guardar) para guardar el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados. Use únicamente el registro de stdout para solucionar problemas de inicio de la aplicación.
>
> Para el registro general en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Registro de depuración del módulo ASP.NET Core (Azure App Service)

El registro de depuración del módulo de ASP.NET Core ofrece un registro adicional, más profundo, del módulo ASP.NET Core. Para habilitar y ver los registros de stdout:

1. Para habilitar el registro de diagnóstico mejorado, realice cualquiera de las acciones siguientes:
   * Siga las instrucciones de [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para configurar la aplicación para un registro de diagnóstico mejorado. Vuelva a implementar la aplicación.
   * Agregue la `<handlerSettings>` que se muestra en [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) al archivo *web.config* de la aplicación activa mediante la consola de Kudu:
     1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
     1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
     1. Abra las carpetas para la ruta de acceso **site** > **wwwroot**. Seleccione el icono de lápiz para editar el archivo *web.config*. Agregue la sección `<handlerSettings>` como se muestra en [Registros de diagnóstico mejorados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Seleccione el botón **Guardar**.
1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra las carpetas para la ruta de acceso **site** > **wwwroot**. Si no ha proporcionado una ruta de acceso para el archivo *aspnetcore debug.log*, el archivo aparece en la lista. Si ha proporcionado una ruta de acceso, vaya a la ubicación del archivo de registro.
1. Abra el archivo de registro con el botón de lápiz situado junto al nombre del archivo.

Deshabilite el registro de depuración una vez haya solucionado los problemas:

Para deshabilitar el registro de depuración mejorado, realice cualquiera de las acciones siguientes:

* Quite localmente la `<handlerSettings>` del archivo *web.config* y vuelva a implementar la aplicación.
* Use la consola de Kudu para editar el archivo *web.config* y quite la sección `<handlerSettings>`. Guarde el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Si no deshabilita el registro de depuración, es posible que se produzcan errores en la aplicación o el servidor. No hay ningún límite para el tamaño del archivo de registro. Use únicamente el registro de depuración para solucionar problemas de inicio de la aplicación.
>
> Para el registro general en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplicación lenta o bloqueada (Azure App Service)

Cuando una aplicación responde con lentitud o se bloquea en una solicitud, consulte los artículos siguientes:

* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) (Uso de la extensión de sitio de diagnóstico de bloqueo para capturar el volcado de memoria para problemas de excepciones intermitentes o problemas de rendimiento en Azure Web Apps)

### <a name="monitoring-blades"></a>Hojas de supervisión

Las hojas de supervisión proporcionan una alternativa a la experiencia de solución de problemas de los métodos descritos anteriormente en el tema. Estas hojas se pueden usar para diagnosticar errores de la serie 500.

Confirme que están instaladas las extensiones de ASP.NET Core. Si no lo están, instálelas manualmente:

1. En la sección de la hoja **HERRAMIENTAS DE DESARROLLO**, seleccione la hoja **Extensiones**.
1. Aparecerán en la lista las **extensiones de ASP.NET Core**.
1. Si las extensiones no están instaladas, seleccione el botón **Add** (Agregar).
1. Elija las **extensiones de ASP.NET Core** de la lista.
1. Seleccione **Aceptar** para aceptar los términos legales.
1. Seleccione **Aceptar** en la hoja **Agregar extensión**.
1. Un mensaje emergente informativo indica si las extensiones se han instalado correctamente.

Si el registro de stdout no está habilitado, siga estos pasos:

1. En Azure Portal, seleccione la hoja **Herramientas avanzadas** en el área **HERRAMIENTAS DE DESARROLLO**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra las carpetas a la ruta de acceso **sitio** > **wwwroot** y desplácese hacia abajo para mostrar el archivo *web.config* en la parte inferior de la lista.
1. Haga clic en el icono de lápiz junto al archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** a: `\\?\%home%\LogFiles\stdout`.
1. Seleccione **Save** (Guardar) para guardar el archivo *web.config* actualizado.

Continúe para activar el registro de diagnóstico:

1. En Azure Portal, seleccione la hoja **Registros de diagnóstico**.
1. Seleccione el conmutador **Activado** en **Registro de la aplicación (sistema de archivos)** y **Mensajes de error detallados**. Seleccione el botón **Guardar** en la parte superior de la hoja.
1. Para incluir el seguimiento de solicitudes con error, también conocido como almacenamiento en búfer de eventos de solicitudes con error (FREB), seleccione el conmutador **Activado** en **Seguimiento de solicitudes con error**.
1. Seleccione la hoja **Secuencia de registro**, que aparece inmediatamente bajo la hoja **Registros de diagnóstico** en el portal.
1. Realice una solicitud a la aplicación.
1. Dentro de los datos de la secuencia de registro, se indica la causa del error.

No olvide deshabilitar el registro de stdout cuando finalice la solución de problemas.

Para ver los registros de seguimiento de solicitudes con error (registros FREB):

1. Vaya a la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) de Azure Portal.
1. Seleccione **Failed Request Tracing Logs** (Registros de seguimiento de solicitudes con error) en el área **SUPPORT TOOLS** (HERRAMIENTAS DE SOPORTE TÉCNICO) de la barra lateral.

Para más información, consulte la [sección sobre los seguimientos de solicitudes con error del tema Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) y el artículo [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure: ¿Cómo se activa el seguimiento de solicitudes con error?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing).

Para más información, consulte [Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Solución de problemas de IIS

### <a name="application-event-log-iis"></a>Registro de eventos de la aplicación (IIS)

Acceda al registro de eventos de la aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error. Los errores tienen un valor de *Módulo AspNetCore de IIS* o *Módulo AspNetCore de IIS Express* en la columna *Origen*.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.

#### <a name="framework-dependent-deployment"></a>Implementación dependiente de marco de trabajo

Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute la aplicación mediante la ejecución del ensamblado de la aplicación con *dotnet.exe*. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `dotnet .\<assembly_name>.dll`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

#### <a name="self-contained-deployment"></a>Implementación autocontenida

Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute el archivo ejecutable de la aplicación. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `<assembly_name>.exe`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

### <a name="aspnet-core-module-stdout-log-iis"></a>Registro de stdout del módulo ASP.NET Core (IIS)

Para habilitar y ver los registros de stdout:

1. Vaya a la carpeta de implementación del sitio en el sistema de hospedaje.
1. Si la carpeta *Logs* no existe, cree la carpeta. Para obtener instrucciones sobre cómo habilitar MSBuild para crear la carpeta *logs* automáticamente en la implementación, consulte el tema [Estructura de directorios](xref:host-and-deploy/directory-structure).
1. Edite el archivo *web.config*. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** para que apunte a la carpeta *logs* (por ejemplo, `.\logs\stdout`). `stdout` en la ruta de acceso es el prefijo del nombre del archivo de registro. Cuando se crea el registro, se agregan automáticamente una marca de tiempo, un identificador de proceso y una extensión de archivo. Cuando se usa `stdout` como prefijo para el nombre de archivo, un archivo de registro se llama normalmente *stdout_20180205184032_5412.log*.
1. Asegúrese de que la identidad del grupo de aplicaciones tiene permisos de escritura en la carpeta *logs*.
1. Guarde el archivo *web.config* actualizado.
1. Realice una solicitud a la aplicación.
1. Vaya a la carpeta *logs*. Busque y abra el registro más reciente de stdout.
1. Estudie el registro para ver los errores.

Deshabilite el registro de stdout una vez haya solucionado los problemas:

1. Edite el archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `false`.
1. Guarde el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Registro de depuración del módulo ASP.NET Core (IIS)

Agregue la siguiente configuración de controlador al archivo *web.config* de la aplicación para habilitar el registro de depuración del módulo ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Confirme que la ruta de acceso especificada para el registro exista y que la identidad del grupo de aplicaciones tenga permisos de escritura en la ubicación.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Habilitación de la página de excepciones para el desarrollador

La [variable de entorno `ASPNETCORE_ENVIRONMENT` se puede agregar a web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para ejecutar la aplicación en el entorno de desarrollo. Siempre y cuando el entorno no se invalide al inicio de la aplicación con `UseEnvironment` en el generador de host, la configuración de la variable de entorno permite que aparezca la [página de excepciones del desarrollador](xref:fundamentals/error-handling) cuando se ejecuta la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Solo se recomienda establecer la variable de entorno para `ASPNETCORE_ENVIRONMENT` cuando se use en servidores de ensayo o pruebas que no estén expuestos a Internet. Quite la variable de entorno del archivo *web.config* cuando termine de solucionar los problemas. Para información sobre la configuración de variables de entorno en *web.config*, consulte el [elemento secundario environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtención de datos de una aplicación

Si una aplicación es capaz de responder a solicitudes, obtenga solicitudes, conexiones y datos adicionales de la aplicación mediante el middleware en línea del terminal. Para obtener más información y un código de ejemplo, vea <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Aplicación lenta o bloqueada (IIS)

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`. El grupo de aplicaciones debe tener acceso de escritura a la carpeta.
1. Ejecute el [script EnableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentación de Azure

* [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sección sobre la depuración remota de las aplicaciones web del artículo Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Introducción a diagnósticos de Azure App Service](/azure/app-service/app-service-diagnostics)
* [Cómo: Supervisar aplicaciones en Azure App Service](/azure/app-service/web-sites-monitor)
* [Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Solucionar los errores HTTP de "502 Puerta de enlace no válida" y "503 Servicio no disponible" en las aplicaciones web de Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) (Espacio aislado de Azure Web App [limitaciones de ejecución del entono de tiempo de ejecución de App Service])
* [Azure Friday: experiencia de diagnóstico y solución de problemas de Azure App Service (vídeo de 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentación de Visual Studio

* [Depuración remota de ASP.NET Core en IIS en Azure en Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Depuración remota de ASP.NET Core en un equipo remoto de IIS en Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Información sobre cómo depurar con Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentación de Visual Studio Code

* [Depuración con Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

En este artículo se proporciona información sobre los errores comunes de inicio de la aplicación e instrucciones sobre cómo diagnosticar errores cuando una aplicación se implementa en Azure App Service o IIS:

[Errores de inicio de aplicación](#app-startup-errors)  
Se explican escenarios comunes de los códigos de estado HTTP de inicio.

[Solución de problemas en Azure App Service](#troubleshoot-on-azure-app-service)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en Azure App Service.

[Solución de problemas de IIS](#troubleshoot-on-iis)  
Se proporcionan consejos para solucionar problemas de las aplicaciones implementadas en IIS o que se ejecutan en IIS Express de forma local. La guía se aplica a las implementaciones de Windows Server y escritorio de Windows.

[Borrado de memorias caché de paquetes](#clear-package-caches)  
Se explica qué hacer cuando hay paquetes incoherentes que interrumpen una aplicación al realizar actualizaciones importantes o cambiar las versiones de los paquetes.

[Recursos adicionales](#additional-resources)  
Se incluyen temas adicionales de solución de problemas.

## <a name="app-startup-errors"></a>Errores de inicio de aplicación

En Visual Studio, un proyecto de ASP.NET Core toma como predeterminado el hospedaje de [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante la depuración. El código de estado *502.5 Error de proceso* que se produce al realizar la depuración localmente se puede diagnosticar si se sigue el consejo de este tema.

### <a name="40314-forbidden"></a>403.14 Prohibido

No se puede iniciar la aplicación. Se registra el siguiente error:

```
The Web server is configured to not list the contents of this directory.
```

El error se suele deber a una implementación interrumpida en el sistema de hospedaje, que incluye cualquiera de los siguientes escenarios:

* La aplicación se implementa en la carpeta incorrecta en el sistema de hospedaje.
* El proceso de implementación no pudo trasladar todos los archivos y las carpetas de la aplicación a la carpeta de implementación en el sistema de hospedaje.
* Falta el archivo *web.config* en la implementación o el contenido del archivo *web.config* no tiene un formato correcto.

Lleve a cabo los siguiente pasos:

1. Elimine todos los archivos y las carpetas de la carpeta de implementación en el sistema de hospedaje.
1. Vuelva a implementar el contenido de la carpeta *publish* de la aplicación en el sistema de hospedaje con un método normal de implementación, como Visual Studio, PowerShell o la implementación manual:
   * Confirme que el archivo *web.config* está en la implementación y que su contenido es correcto.
   * Cuando hospede contenido en Azure App Service, confirme que la aplicación se implementa en la carpeta `D:\home\site\wwwroot`.
   * Si IIS hospeda la aplicación, confirme que la aplicación se implementa en la **ruta de acceso física** de IIS que se muestra en la **configuración básica** del **administrador de IIS**.
1. Confirme que todos los archivos y las carpetas de la aplicación se han implementado; para ello, compare la implementación en el sistema de hospedaje con el contenido de la carpeta *publish* del proyecto.

Para obtener más información sobre el diseño de una aplicación ASP.NET Core publicada, consulte <xref:host-and-deploy/directory-structure>. Para obtener más información sobre el archivo *web.config*, vea <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.

### <a name="500-internal-server-error"></a>500 Error interno del servidor

La aplicación se inicia, pero un error impide que el servidor complete la solicitud.

Este error se produce dentro del código de la aplicación durante el inicio o mientras se crea una respuesta. La respuesta no puede contener nada o puede aparecer como *500 Error interno del servidor* en el explorador. El registro de eventos de la aplicación normalmente indica que la aplicación se ha iniciado normalmente. Desde la perspectiva del servidor, eso es correcto. La aplicación se inició, pero no puede generar una respuesta válida. Ejecute la aplicación en un símbolo del sistema en el servidor o habilite el registro de stdout del módulo ASP.NET Core para solucionar el problema.

### <a name="5025-process-failure"></a>502.5 Error de proceso

El proceso de trabajo no funciona. La aplicación no se inicia.

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) intenta iniciar el proceso de trabajo, pero no lo consigue. La causa del error de inicio del proceso se suele determinar a partir de las entradas del registro de eventos de la aplicación y del registro de stdout del módulo ASP.NET Core.

Una condición de error habitual es que la aplicación esté mal configurada porque tiene como destino una versión del marco compartido de ASP.NET Core que no está presente. Compruebe qué versiones del marco compartido de ASP.NET Core están instaladas en el equipo de destino. El *marco de trabajo compartido* es el conjunto de ensamblados (archivos *.dll*) que se instalan en la máquina, y se hace referencia a ellos mediante un metapaquete como `Microsoft.AspNetCore.App`. La referencia del metapaquete puede especificar una versión mínima necesaria. Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La página de error *502.5 Error de proceso* se devuelve cuando el proceso de trabajo no se puede iniciar debido a un error de configuración de la aplicación o del hospedaje:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>No se pudo iniciar la aplicación (código de error "0x800700c1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Error al iniciar la aplicación porque el ensamblado de la aplicación ( *.dll*) no se ha podido cargar.

Este error se produce cuando hay un error de coincidencia del valor de bits entre la aplicación publicada y el proceso w3wp/iisexpress.

Confirme que la opción de 32 bits del grupo de aplicaciones sea correcta:

1. Seleccione el grupo de aplicaciones en **Grupos de aplicaciones** del Administrador de IIS.
1. Seleccione **Configuración avanzada** en **Modificar grupo de aplicaciones**, en el panel **Acciones**.
1. Establezca **Habilitar aplicaciones de 32 bits**:
   * Si implementa una aplicación de 32 bits (x86), establezca el valor en `True`.
   * Si implementa una aplicación de 64 bits (x64), establezca el valor en `False`.

Confirme que no hay ningún conflicto entre una propiedad `<Platform>` de MSBuild en el archivo del proyecto y el valor de bits publicado de la aplicación.

### <a name="connection-reset"></a>Restablecimiento de la conexión

Si se produce un error después de que se envían los encabezados, el servidor no tiene tiempo para enviar un mensaje **500 Error interno del servidor** cuando se produce un error. Esto suele ocurrir cuando se produce un error durante la serialización de objetos complejos en una respuesta. Este tipo de error aparece como un error de *restablecimiento de la conexión* en el cliente. El [Registro de aplicaciones](xref:fundamentals/logging/index) puede ayudar a solucionar estos tipos de errores.

### <a name="default-startup-limits"></a>Límites de inicio predeterminados

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) está configurado con un valor predeterminado de *startupTimeLimit* de 120 segundos. Cuando se deja en el valor predeterminado, una aplicación puede tardar hasta dos minutos en iniciarse antes de que el módulo registre un error de proceso. Para información sobre la configuración del módulo, consulte [Atributos del elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Solución de problemas en Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Registro de eventos de la aplicación (Azure App Service)

Para acceder al registro de eventos de la aplicación, use la hoja **Diagnose and solve problems** (Diagnosticar y solucionar problemas) de Azure Portal:

1. En Azure Portal, abra la aplicación en **App Services**.
1. Seleccione **Diagnosticar y solucionar problemas**.
1. Seleccione el título **Herramientas de diagnóstico**.
1. En **Herramientas de soporte técnico**, seleccione el botón **Eventos de la aplicación**.
1. Examine el error más reciente que hayan proporcionado las entradas *IIS AspNetCoreModule* o *IIS AspNetCoreModule V2* en la columna **Origen**.

Una alternativa al uso de la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) es examinar el archivo de registro de eventos de la aplicación directamente mediante [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra la carpeta **LogFiles**.
1. Seleccione el icono de lápiz junto al archivo *eventlog.xml*.
1. Examine el registro. Desplácese al final del registro para ver los eventos más recientes.

### <a name="run-the-app-in-the-kudu-console"></a>Ejecución de la aplicación en la consola de Kudu

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. Puede ejecutar la aplicación en la consola de ejecución remota de [Kudu](https://github.com/projectkudu/kudu/wiki) para detectar el error:

1. Abra **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Prueba de una aplicación de 32 bits (x86)

**Versión actual**

1. `cd d:\home\site\wwwroot`
1. Ejecute la aplicación:
   * Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x86) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Prueba de una aplicación de 64 bits (x64)

**Versión actual**

* Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):
  1. `cd D:\Program Files\dotnet`
  1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Ejecute la aplicación: `{ASSEMBLY NAME}.exe`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

**Implementación dependiente del marco en ejecución en una versión preliminar**

*Requiere la instalación de la extensión de sitio ASP.NET Core {VERSION} (x64) Runtime.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` es la versión del runtime)
1. Ejecute la aplicación: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La salida de consola de la aplicación, que muestra los posibles errores, se canaliza a la consola de Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Registro de stdout del módulo ASP.NET Core (Azure App Service)

El registro stdout del módulo ASP.NET Core con frecuencia registra mensajes de error útiles que no se encuentran en el registro de eventos de la aplicación. Para habilitar y ver los registros de stdout:

1. Vaya a la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) de Azure Portal.
1. En **Seleccione una categoría de problema**, seleccione el botón abajo **Aplicación web**.
1. En **Suggested Solutions** (Soluciones sugeridas) > **Enable Stdout Log Redirection** (Habilitar el redireccionamiento de registros de stdout), seleccione el botón para **abrir la consola de Kudu y editar web.config**.
1. En la **consola de diagnóstico** de Kudu, abra las carpetas para la ruta de acceso **site** > **wwwroot**. Desplácese hacia abajo para mostrar el archivo *web.config* en la parte inferior de la lista.
1. Haga clic en el icono de lápiz junto al archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** a: `\\?\%home%\LogFiles\stdout`.
1. Seleccione **Save** (Guardar) para guardar el archivo *web.config* actualizado.
1. Realice una solicitud a la aplicación.
1. Vuelva a Azure Portal. Seleccione la hoja **Herramientas avanzadas** en el área **Herramientas de desarrollo**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Seleccione la carpeta **LogFiles**.
1. Inspeccione la columna **Modificado** y seleccione el icono de lápiz para editar el registro de stdout con la última fecha de modificación.
1. Cuando se abre el archivo de registro, se muestra el error.

Deshabilite el registro de stdout una vez haya solucionado los problemas:

1. En la **consola de diagnóstico** de Kudu, vuelva a la ruta de acceso **site** > **wwwroot** para mostrar el archivo *web.config*. Seleccione el icono de lápiz para abrir de nuevo el archivo **web.config**.
1. Establezca **stdoutLogEnabled** en `false`.
1. Seleccione **Save** (Guardar) para guardar el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados. Use únicamente el registro de stdout para solucionar problemas de inicio de la aplicación.
>
> Para el registro general en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplicación lenta o bloqueada (Azure App Service)

Cuando una aplicación responde con lentitud o se bloquea en una solicitud, consulte los artículos siguientes:

* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/) (Uso de la extensión de sitio de diagnóstico de bloqueo para capturar el volcado de memoria para problemas de excepciones intermitentes o problemas de rendimiento en Azure Web Apps)

### <a name="monitoring-blades"></a>Hojas de supervisión

Las hojas de supervisión proporcionan una alternativa a la experiencia de solución de problemas de los métodos descritos anteriormente en el tema. Estas hojas se pueden usar para diagnosticar errores de la serie 500.

Confirme que están instaladas las extensiones de ASP.NET Core. Si no lo están, instálelas manualmente:

1. En la sección de la hoja **HERRAMIENTAS DE DESARROLLO**, seleccione la hoja **Extensiones**.
1. Aparecerán en la lista las **extensiones de ASP.NET Core**.
1. Si las extensiones no están instaladas, seleccione el botón **Add** (Agregar).
1. Elija las **extensiones de ASP.NET Core** de la lista.
1. Seleccione **Aceptar** para aceptar los términos legales.
1. Seleccione **Aceptar** en la hoja **Agregar extensión**.
1. Un mensaje emergente informativo indica si las extensiones se han instalado correctamente.

Si el registro de stdout no está habilitado, siga estos pasos:

1. En Azure Portal, seleccione la hoja **Herramientas avanzadas** en el área **HERRAMIENTAS DE DESARROLLO**. Seleccione el botón **Ir&rarr;** . Se abre la consola de Kudu en una nueva pestaña o ventana del explorador.
1. Mediante la barra de navegación de la parte superior de la página, abra la **consola de depuración** y seleccione **CMD**.
1. Abra las carpetas a la ruta de acceso **sitio** > **wwwroot** y desplácese hacia abajo para mostrar el archivo *web.config* en la parte inferior de la lista.
1. Haga clic en el icono de lápiz junto al archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** a: `\\?\%home%\LogFiles\stdout`.
1. Seleccione **Save** (Guardar) para guardar el archivo *web.config* actualizado.

Continúe para activar el registro de diagnóstico:

1. En Azure Portal, seleccione la hoja **Registros de diagnóstico**.
1. Seleccione el conmutador **Activado** en **Registro de la aplicación (sistema de archivos)** y **Mensajes de error detallados**. Seleccione el botón **Guardar** en la parte superior de la hoja.
1. Para incluir el seguimiento de solicitudes con error, también conocido como almacenamiento en búfer de eventos de solicitudes con error (FREB), seleccione el conmutador **Activado** en **Seguimiento de solicitudes con error**.
1. Seleccione la hoja **Secuencia de registro**, que aparece inmediatamente bajo la hoja **Registros de diagnóstico** en el portal.
1. Realice una solicitud a la aplicación.
1. Dentro de los datos de la secuencia de registro, se indica la causa del error.

No olvide deshabilitar el registro de stdout cuando finalice la solución de problemas.

Para ver los registros de seguimiento de solicitudes con error (registros FREB):

1. Vaya a la hoja **Diagnose and solve problems** (Diagnosticar y resolver problemas) de Azure Portal.
1. Seleccione **Failed Request Tracing Logs** (Registros de seguimiento de solicitudes con error) en el área **SUPPORT TOOLS** (HERRAMIENTAS DE SOPORTE TÉCNICO) de la barra lateral.

Para más información, consulte la [sección sobre los seguimientos de solicitudes con error del tema Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) y el artículo [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure: ¿Cómo se activa el seguimiento de solicitudes con error?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing).

Para más información, consulte [Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Solución de problemas de IIS

### <a name="application-event-log-iis"></a>Registro de eventos de la aplicación (IIS)

Acceda al registro de eventos de la aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error. Los errores tienen un valor de *Módulo AspNetCore de IIS* o *Módulo AspNetCore de IIS Express* en la columna *Origen*.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos de la aplicación. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.

#### <a name="framework-dependent-deployment"></a>Implementación dependiente de marco de trabajo

Si la aplicación es una [implementación dependiente del marco](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute la aplicación mediante la ejecución del ensamblado de la aplicación con *dotnet.exe*. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `dotnet .\<assembly_name>.dll`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

#### <a name="self-contained-deployment"></a>Implementación autocontenida

Si la aplicación es una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd):

1. En un símbolo del sistema, vaya a la carpeta de implementación y ejecute el archivo ejecutable de la aplicación. En el siguiente comando, sustituya el nombre del ensamblado de la aplicación por \<assembly_name>: `<assembly_name>.exe`.
1. La salida de consola de la aplicación, que muestra los posibles errores, se escribe en la ventana de la consola.
1. Si los errores se producen al realizar una solicitud a la aplicación, realice una solicitud al host y el puerto donde escucha Kestrel. Con el host y el puerto predeterminados, realizar una solicitud a `http://localhost:5000/`. Si la aplicación responde normalmente en la dirección del punto de conexión de Kestrel, es más probable que el problema esté relacionado con la configuración de hospedaje que con la propia aplicación.

### <a name="aspnet-core-module-stdout-log-iis"></a>Registro de stdout del módulo ASP.NET Core (IIS)

Para habilitar y ver los registros de stdout:

1. Vaya a la carpeta de implementación del sitio en el sistema de hospedaje.
1. Si la carpeta *Logs* no existe, cree la carpeta. Para obtener instrucciones sobre cómo habilitar MSBuild para crear la carpeta *logs* automáticamente en la implementación, consulte el tema [Estructura de directorios](xref:host-and-deploy/directory-structure).
1. Edite el archivo *web.config*. Establezca **stdoutLogEnabled** en `true` y cambie la ruta de acceso de **stdoutLogFile** para que apunte a la carpeta *logs* (por ejemplo, `.\logs\stdout`). `stdout` en la ruta de acceso es el prefijo del nombre del archivo de registro. Cuando se crea el registro, se agregan automáticamente una marca de tiempo, un identificador de proceso y una extensión de archivo. Cuando se usa `stdout` como prefijo para el nombre de archivo, un archivo de registro se llama normalmente *stdout_20180205184032_5412.log*.
1. Asegúrese de que la identidad del grupo de aplicaciones tiene permisos de escritura en la carpeta *logs*.
1. Guarde el archivo *web.config* actualizado.
1. Realice una solicitud a la aplicación.
1. Vaya a la carpeta *logs*. Busque y abra el registro más reciente de stdout.
1. Estudie el registro para ver los errores.

Deshabilite el registro de stdout una vez haya solucionado los problemas:

1. Edite el archivo *web.config*.
1. Establezca **stdoutLogEnabled** en `false`.
1. Guarde el archivo.

Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> La imposibilidad de deshabilitar el registro de stdout puede dar lugar a un error de la aplicación o del servidor. No hay ningún límite en el tamaño del archivo de registro ni en el número de archivos de registro creados.
>
> Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Habilitación de la página de excepciones para el desarrollador

La [variable de entorno `ASPNETCORE_ENVIRONMENT` se puede agregar a web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para ejecutar la aplicación en el entorno de desarrollo. Siempre y cuando el entorno no se invalide al inicio de la aplicación con `UseEnvironment` en el generador de host, la configuración de la variable de entorno permite que aparezca la [página de excepciones del desarrollador](xref:fundamentals/error-handling) cuando se ejecuta la aplicación.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Solo se recomienda establecer la variable de entorno para `ASPNETCORE_ENVIRONMENT` cuando se use en servidores de ensayo o pruebas que no estén expuestos a Internet. Quite la variable de entorno del archivo *web.config* cuando termine de solucionar los problemas. Para información sobre la configuración de variables de entorno en *web.config*, consulte el [elemento secundario environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtención de datos de una aplicación

Si una aplicación es capaz de responder a solicitudes, obtenga solicitudes, conexiones y datos adicionales de la aplicación mediante el middleware en línea del terminal. Para obtener más información y un código de ejemplo, vea <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Aplicación lenta o bloqueada (IIS)

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`. El grupo de aplicaciones debe tener acceso de escritura a la carpeta.
1. Ejecute el [script EnableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Si la aplicación usa el [modelo de hospedaje en proceso](xref:host-and-deploy/iis/index#in-process-hosting-model), ejecute el script *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si la aplicación usa el [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ejecute el script *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentación de Azure

* [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sección sobre la depuración remota de las aplicaciones web del artículo Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Introducción a diagnósticos de Azure App Service](/azure/app-service/app-service-diagnostics)
* [Cómo: Supervisar aplicaciones en Azure App Service](/azure/app-service/web-sites-monitor)
* [Solución de problemas de una aplicación web en Azure App Service con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Solucionar los errores HTTP de "502 Puerta de enlace no válida" y "503 Servicio no disponible" en las aplicaciones web de Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Preguntas más frecuentes sobre el rendimiento de aplicaciones para Web Apps de Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) (Espacio aislado de Azure Web App [limitaciones de ejecución del entono de tiempo de ejecución de App Service])
* [Azure Friday: experiencia de diagnóstico y solución de problemas de Azure App Service (vídeo de 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentación de Visual Studio

* [Depuración remota de ASP.NET Core en IIS en Azure en Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Depuración remota de ASP.NET Core en un equipo remoto de IIS en Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Información sobre cómo depurar con Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentación de Visual Studio Code

* [Depuración con Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
