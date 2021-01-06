---
title: Hospedaje de ASP.NET Core en un servicio de Windows
author: rick-anderson
description: Aprenda a hospedar una aplicación ASP.NET Core en un servicio de Windows.
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 31a738e7aa8779171dfa09a5678d7240b8f62343
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057237"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedaje de ASP.NET Core en un servicio de Windows

::: moniker range=">= aspnetcore-3.0"

Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

* [ASP.NET Core SDK 2.1 o posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 o posterior](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Plantilla Worker Service

La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración. Para usar la plantilla como base de una aplicación de servicio de Windows:

1. Cree una aplicación Worker Service con la plantilla de .NET Core.
1. Siga las instrucciones de la sección [Configuración de aplicaciones](#app-configuration) para actualizar la aplicación Worker Service a fin de que se ejecute como un servicio de Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Configuración de aplicaciones

La aplicación requiere una referencia de paquete para [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

Se llama a `IHostBuilder.UseWindowsService` al compilar el host. Si la aplicación se ejecuta como un servicio de Windows, el método:

* Establece la vigencia del host en `WindowsServiceLifetime`.
* Establece la [raíz del contenido](xref:fundamentals/index#content-root) en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).
* Habilita el registro en el registro de eventos:
  * El nombre de la aplicación se usa como nombre de origen predeterminado.
  * El nivel de registro predeterminado es *Advertencia* o superior para una aplicación basada en una plantilla de ASP.NET Core que llama a `CreateDefaultBuilder` con el fin de compilar el host.
  * Invalide el nivel de registro predeterminado con la clave `Logging:EventLog:LogLevel:Default` en *appsettings.json* /*appsettings.{Environment}.json* u otro proveedor de configuración.
  * Los administradores son los únicos que pueden crear nuevos orígenes de eventos. Cuando no se puede crear un origen de eventos con el nombre de la aplicación, se registra una advertencia para el origen *Aplicación* y los registros de eventos se deshabilitan.

En `CreateHostBuilder` de *Program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Las aplicaciones de ejemplo siguientes acompañan a este tema:

* Ejemplo de Background Worker Service: un ejemplo de una aplicación que no es para la Web basado en la [plantilla Worker Service](#worker-service-template) que usa [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.
* Ejemplo de App Service web: un ejemplo de aplicación web de Razor Pages que se ejecuta como un servicio de Windows con [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.

Para obtener instrucciones sobre MVC, vea los artículos en <xref:mvc/overview> y <xref:migration/22-to-30>.

## <a name="deployment-type"></a>Tipo de implementación

Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Implementación dependiente de marco (FDD)

La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino. Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.

Si se usa el [SDK web](#sdk), para una aplicación de Windows Services no se requiere un archivo *web.config*, que normalmente se crea cuando se publica una aplicación ASP.NET Core. Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Implementación autocontenida (SCD)

Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host. El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.

Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para varios RID:

* Proporcione los RID en una lista delimitada por punto y coma.
* Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Cuenta de usuario de servicio

Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.

En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.

A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.

Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).

Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas. Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).

## <a name="log-on-as-a-service-rights"></a>Derechos para iniciar sesión como servicio

Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:

1. Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.
1. Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.
1. Abra la directiva **Iniciar sesión como servicio**.
1. Seleccione **Agregar usuario o grupo**.
1. Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:
   1. Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.
   1. Seleccione **Opciones avanzadas**. Seleccione **Buscar ahora**. Seleccione la cuenta de usuario de la lista. Seleccione **Aceptar**. Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.
1. Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.

## <a name="create-and-manage-the-windows-service"></a>Creación y administración del servicio de Windows

### <a name="create-a-service"></a>Creación de un servicio

Use los comandos de PowerShell para registrar un servicio. Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`). No incluya el archivo ejecutable de la aplicación en la ruta de acceso. No se requiere una barra diagonal al final.
* `{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).
* `{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`). Incluya el nombre de archivo del ejecutable con la extensión.
* `{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).
* `{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).

### <a name="start-a-service"></a>iniciar un servicio.

Inicie el servicio con el siguiente comando de PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Este comando tarda unos segundos en iniciar el servicio.

### <a name="determine-a-services-status"></a>Determinación del estado de un servicio

Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

El estado se notifica como uno de los siguientes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Detención de un servicio

Detenga un servicio con el siguiente comando de PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Eliminación de un servicio

Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional. Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuración de puntos de conexión

ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada. Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.

Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS. Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.

> [!NOTE]
> No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.

## <a name="current-directory-and-content-root"></a>Directorio actual y raíz del contenido

El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*. La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración). Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Uso de ContentRootPath o ContentRootFileProvider

Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para buscar los recursos de una aplicación.

Cuando la aplicación se ejecuta como un servicio, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> establece la ruta de acceso <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Los archivos de configuración predeterminados de la aplicación, *appsettings.json* y *appsettings.{Environment}.json*, se cargan desde la raíz del contenido de la aplicación mediante una llamada a [CreateDefaultBuilder durante la construcción del host](xref:fundamentals/host/generic-host#set-up-a-host).

En el caso de otros archivos de configuración cargados por el código para desarrolladores en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, no es necesario llamar a <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. En el ejemplo siguiente, el archivo *custom_settings.json* ya se encuentra en la raíz del contenido de la aplicación y se carga sin establecer explícitamente una ruta de acceso base:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

No intente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obtener una ruta de acceso a recursos porque una aplicación de servicio de Windows devuelve la carpeta *C:\\WINDOWS\\system32* como su directorio actual.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco

Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Errores comunes

* Se está usando una versión preliminar o antigua de PowerShell.
* El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish). La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones. Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:
  * *bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)
  * *bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)
* El servicio no está en el estado EN EJECUCIÓN.
* Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas. La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.
* El usuario no tiene derechos de *inicio de sesión como servicio*.
* La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.
* La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).
* El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.

### <a name="system-and-application-event-logs"></a>Registros de eventos del sistema y de aplicación

Acceda a los registros de eventos del sistema y de aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Sistema** para abrir el registro de eventos del sistema. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje. Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

### <a name="slow-or-hanging-app"></a>Aplicación lenta o bloqueada

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.
1. Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="additional-resources"></a>Recursos adicionales

* [Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

* [ASP.NET Core SDK 2.1 o posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 o posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuración de aplicaciones

La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola. Compruebe si el depurador está asociado o hay presente un conmutador `--console`. Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):

* Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación. No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>. Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root). Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.
* Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.

Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.

Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.

En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación. Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implementación

Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Implementación dependiente de marco (FDD)

La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino. Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.

El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino. En el ejemplo siguiente, el RID se establece en `win7-x64`. La propiedad `<SelfContained>` se establece en `false`. Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe*) para Windows y una aplicación que depende del marco .NET Core compartido.

No se requiere un archivo *web.config*, que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services. Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Implementación autocontenida (SCD)

Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host. El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.

Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para varios RID:

* Proporcione los RID en una lista delimitada por punto y coma.
* Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).

Una propiedad `<SelfContained>` se establece en `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Cuenta de usuario de servicio

Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.

En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.

A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.

Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).

Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas. Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).

## <a name="log-on-as-a-service-rights"></a>Derechos para iniciar sesión como servicio

Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:

1. Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.
1. Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.
1. Abra la directiva **Iniciar sesión como servicio**.
1. Seleccione **Agregar usuario o grupo**.
1. Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:
   1. Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.
   1. Seleccione **Opciones avanzadas**. Seleccione **Buscar ahora**. Seleccione la cuenta de usuario de la lista. Seleccione **Aceptar**. Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.
1. Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.

## <a name="create-and-manage-the-windows-service"></a>Creación y administración del servicio de Windows

### <a name="create-a-service"></a>Creación de un servicio

Use los comandos de PowerShell para registrar un servicio. Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`). No incluya el archivo ejecutable de la aplicación en la ruta de acceso. No se requiere una barra diagonal al final.
* `{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).
* `{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`). Incluya el nombre de archivo del ejecutable con la extensión.
* `{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).
* `{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).

### <a name="start-a-service"></a>iniciar un servicio.

Inicie el servicio con el siguiente comando de PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Este comando tarda unos segundos en iniciar el servicio.

### <a name="determine-a-services-status"></a>Determinación del estado de un servicio

Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

El estado se notifica como uno de los siguientes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Detención de un servicio

Detenga un servicio con el siguiente comando de PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Eliminación de un servicio

Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Control de los eventos de inicio y detención

Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional. Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuración de puntos de conexión

ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada. Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.

Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS. Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.

> [!NOTE]
> No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.

## <a name="current-directory-and-content-root"></a>Directorio actual y raíz del contenido

El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*. La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración). Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio. En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.

En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco

Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Errores comunes

* Se está usando una versión preliminar o antigua de PowerShell.
* El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish). La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones. Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:
  * *bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)
  * *bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)
* El servicio no está en el estado EN EJECUCIÓN.
* Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas. La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.
* El usuario no tiene derechos de *inicio de sesión como servicio*.
* La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.
* La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).
* El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.

### <a name="system-and-application-event-logs"></a>Registros de eventos del sistema y de aplicación

Acceda a los registros de eventos del sistema y de aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Sistema** para abrir el registro de eventos del sistema. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje. Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

### <a name="slow-or-hanging-app"></a>Aplicación lenta o bloqueada

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.
1. Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="additional-resources"></a>Recursos adicionales

* [Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications). Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

* [ASP.NET Core SDK 2.1 o posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 o posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuración de aplicaciones

La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola. Compruebe si el depurador está asociado o hay presente un conmutador `--console`. Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):

* Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación. No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>. Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root). Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.
* Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.

Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.

Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.

En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación. Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implementación

Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Implementación dependiente de marco (FDD)

La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino. Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe*), denominado *ejecutable dependiente del marco*.

El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino. En el ejemplo siguiente, el RID se establece en `win7-x64`. La propiedad `<SelfContained>` se establece en `false`. Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe*) para Windows y una aplicación que depende del marco .NET Core compartido.

La propiedad `<UseAppHost>` se establece en `true`. Esta propiedad proporciona el servicio con una ruta de acceso de activación (un archivo ejecutable, *.exe*) para una FDD.

No se requiere un archivo *web.config*, que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services. Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Implementación autocontenida (SCD)

Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host. El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.

Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para varios RID:

* Proporcione los RID en una lista delimitada por punto y coma.
* Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).

Una propiedad `<SelfContained>` se establece en `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Cuenta de usuario de servicio

Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.

En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.

A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.

Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).

Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas. Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).

## <a name="log-on-as-a-service-rights"></a>Derechos para iniciar sesión como servicio

Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:

1. Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.
1. Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.
1. Abra la directiva **Iniciar sesión como servicio**.
1. Seleccione **Agregar usuario o grupo**.
1. Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:
   1. Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.
   1. Seleccione **Opciones avanzadas**. Seleccione **Buscar ahora**. Seleccione la cuenta de usuario de la lista. Seleccione **Aceptar**. Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.
1. Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.

## <a name="create-and-manage-the-windows-service"></a>Creación y administración del servicio de Windows

### <a name="create-a-service"></a>Creación de un servicio

Use los comandos de PowerShell para registrar un servicio. Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`). No incluya el archivo ejecutable de la aplicación en la ruta de acceso. No se requiere una barra diagonal al final.
* `{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).
* `{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`). Incluya el nombre de archivo del ejecutable con la extensión.
* `{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).
* `{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).

### <a name="start-a-service"></a>iniciar un servicio.

Inicie el servicio con el siguiente comando de PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Este comando tarda unos segundos en iniciar el servicio.

### <a name="determine-a-services-status"></a>Determinación del estado de un servicio

Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

El estado se notifica como uno de los siguientes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Detención de un servicio

Detenga un servicio con el siguiente comando de PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Eliminación de un servicio

Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Control de los eventos de inicio y detención

Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional. Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuración de puntos de conexión

ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada. Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.

Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS. Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.

> [!NOTE]
> No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.

## <a name="current-directory-and-content-root"></a>Directorio actual y raíz del contenido

El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*. La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración). Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio. En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.

En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco

Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Errores comunes

* Se está usando una versión preliminar o antigua de PowerShell.
* El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish). La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones. Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:
  * *bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)
  * *bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)
* El servicio no está en el estado EN EJECUCIÓN.
* Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas. La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.
* El usuario no tiene derechos de *inicio de sesión como servicio*.
* La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.
* La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).
* El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.

### <a name="system-and-application-event-logs"></a>Registros de eventos del sistema y de aplicación

Acceda a los registros de eventos del sistema y de aplicación:

1. Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.
1. En **Visor de eventos**, abra el nodo **Registros de Windows**.
1. Seleccione **Sistema** para abrir el registro de eventos del sistema. Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.
1. Busque los errores asociados a la aplicación objeto del error.

### <a name="run-the-app-at-a-command-prompt"></a>Ejecución de la aplicación en un símbolo del sistema

Muchos errores de inicio no generan información útil en el registro de eventos. La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje. Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Borrado de memorias caché de paquetes

Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación. En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes. La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:

1. Elimine las carpetas *bin* y *obj*.
1. Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.

   Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`. *nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).

1. Restaure el proyecto y vuelva a compilarlo.
1. Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.

### <a name="slow-or-hanging-app"></a>Aplicación lenta o bloqueada

Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.

#### <a name="app-crashes-or-encounters-an-exception"></a>Bloqueo o excepción de la aplicación

Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.
1. Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.
1. Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad. El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.

> [!WARNING]
> Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad

Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.

#### <a name="analyze-the-dump"></a>Análisis del volcado de memoria

El volcado de memoria se puede analizar de varias maneras. Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).

## <a name="additional-resources"></a>Recursos adicionales

* [Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
