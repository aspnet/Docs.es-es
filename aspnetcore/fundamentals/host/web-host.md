---
title: Host web de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el host web en ASP.NET Core, que es responsable de la administración de inicio y duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 98be96bf60441cf09a315dbd1c60e109a7a08afe
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253116"
---
# <a name="aspnet-core-web-host"></a>Host web de ASP.NET Core

Las aplicaciones de ASP.NET Core configuran e inician un *host*. El host es responsable de la administración del inicio y la duración de la aplicación. Como mínimo, el host configura un servidor y una canalización de procesamiento de solicitudes. El host también puede configurar el registro, la inserción de dependencias y la configuración.

::: moniker range=">= aspnetcore-3.0"

En el artículo se explica el host web, que solo permanece disponible para compatibilidad con versiones anteriores: Se recomienda el [host genérico](xref:fundamentals/host/generic-host) para todos los tipos de aplicaciones.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se describe el host web, que sirve para hospedar aplicaciones web. Para otros tipos de aplicaciones, use el [host genérico](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Configuración de un host

Cree un host con una instancia de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Normalmente, esto se realiza en el punto de entrada de la aplicación, el método `Main`.

En las plantillas de proyecto, `Main` se encuentra en *Program.cs*. Una aplicación típica llama a [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para empezar a configurar un host:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

El código que llama a `CreateDefaultBuilder` está en un método denominado `CreateWebHostBuilder`, que lo diferencia del código de `Main` que llama a `Run` en el objeto generador. Dicha diferencia es necesaria si usa [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/). Las herramientas esperan encontrar un método `CreateWebHostBuilder` al que pueden llamar en tiempo de diseño para configurar el host sin ejecutar la aplicación. Una alternativa consiste en implementar `IDesignTimeDbContextFactory`. Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).

`CreateDefaultBuilder` realiza las tareas siguientes:

::: moniker range=">= aspnetcore-5.0"
* Configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web por medio de los proveedores de configuración de hospedaje de la aplicación. Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel/options>.
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* Configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web por medio de los proveedores de configuración de hospedaje de la aplicación. Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.
::: moniker-end
* Establece la [raíz del contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Carga la [configuración de host](#host-configuration-values) de:
  * Variables de entorno con el prefijo `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`).
  * Argumentos de la línea de comandos.
* Carga la configuración de la aplicación en el siguiente orden:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Secretos del usuario](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.
  * Variables de entorno.
  * Argumentos de la línea de comandos.
* Configura el [registro](xref:fundamentals/logging/index) para la salida de consola y de depuración. El registro incluye reglas de [filtrado del registro](xref:fundamentals/logging/index#log-filtering) especificadas en una sección de configuración de registro de un archivo *appsettings.json* o *appsettings.{Environment}.json*.
* Cuando se ejecuta detrás de IIS con el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` habilita la [integración de IIS](xref:host-and-deploy/iis/index), que configura la dirección base y el puerto de la aplicación. La integración de IIS también configura la aplicación para que [capture errores de inicio](#capture-startup-errors). Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.
* Establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo. Para más información, vea [Validación del ámbito](#scope-validation).

La configuración definida en `CreateDefaultBuilder` se puede reemplazar y aumentar mediante [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) y otros métodos y métodos de extensión de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). A continuación, se presentan algunos ejemplos:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se usa para especificar `IConfiguration` adicionales para la aplicación. La siguiente llamada `ConfigureAppConfiguration` agrega un delegado para incluir la configuración de la aplicación en el archivo *appsettings.xml*. Es posible llamar a `ConfigureAppConfiguration` varias veces. Tenga en cuenta que esta configuración no se aplica al host (por ejemplo, direcciones URL de servidor o entorno). Vea la sección [Valores de configuración de host](#host-configuration-values).

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* La siguiente llamada `ConfigureLogging` agrega un delegado para configurar el nivel de registro mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) en [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel). Esta configuración reemplaza la configuración de *appsettings.Development.JSON* (`LogLevel.Debug`) y *appsettings.Production.JSON* (`LogLevel.Error`) configurada mediante `CreateDefaultBuilder`. Es posible llamar a `ConfigureLogging` varias veces.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* La siguiente llamada a `ConfigureKestrel` reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* La siguiente llamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

La [raíz del contenido](xref:fundamentals/index#content-root) determina la ubicación en la que el host busca archivos de contenido como, por ejemplo, archivos de vista MVC. Cuando la aplicación se inicia desde la carpeta raíz del proyecto, esta se utiliza como la raíz del contenido. Este es el valor predeterminado usado en [Visual Studio](https://visualstudio.microsoft.com) y las [nuevas plantillas dotnet](/dotnet/core/tools/dotnet-new).

Para obtener más información sobre la configuración de la aplicación, vea <xref:fundamentals/configuration/index>.

> [!NOTE]
> Como alternativa al uso del método estático `CreateDefaultBuilder`, crear un host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) es un enfoque compatible con ASP.NET Core 2.x.

Al configurar un host, se pueden proporcionar los métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) y [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices). Si se especifica una clase `Startup`, debe definir un método `Configure`. Para obtener más información, vea <xref:fundamentals/startup>. Varias llamadas a `ConfigureServices` se anexan entre sí. Varias llamadas a `Configure` o `UseStartup` en el `WebHostBuilder` reemplazan la configuración anterior.

## <a name="host-configuration-values"></a>Valores de configuración de host

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) se basa en los siguientes métodos para establecer los valores de configuración del host:

* Configuración del generador de host, que incluye las variables de entorno con el formato `ASPNETCORE_{configurationKey}`. Por ejemplo: `ASPNETCORE_ENVIRONMENT`.
* Extensiones como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) y [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (vea la sección [Invalidación de la configuración](#override-configuration)).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) y la clave asociada. Al establecer un valor con `UseSetting`, el valor se establece como una cadena, independientemente del tipo.

El host usa cualquier opción que establece un valor en último lugar. Para obtener más información, consulte [Invalidación de la configuración](#override-configuration) en la sección siguiente.

### <a name="application-key-name"></a>Clave de aplicación (nombre)

::: moniker range=">= aspnetcore-3.0"

La propiedad `IWebHostEnvironment.ApplicationName` se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host. El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación. Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La propiedad [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host. El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación. Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

**Clave**: nombreDeAplicación  
**Tipo**: *cadena*  
**Valor predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.  
**Establecer mediante**: `UseSetting`  
**Variable de entorno**: `ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Capturar errores de inicio

Esta configuración controla la captura de errores de inicio.

**Clave**: captureStartupErrors  
**Tipo**: *bool* (`true` o `1`)  
**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.  
**Establecer mediante**: `CaptureStartupErrors`  
**Variable de entorno**: `ASPNETCORE_CAPTURESTARTUPERRORS`

Cuando es `false`, los errores durante el inicio provocan la salida del host. Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Raíz del contenido

Esta configuración determina la ubicación en la que ASP.NET Core comienza a buscar archivos de contenido.

**Clave**: contentRoot  
**Tipo**: *cadena*  
**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.  
**Establecer mediante**: `UseContentRoot`  
**Variable de entorno**: `ASPNETCORE_CONTENTROOT`

La raíz del contenido también se usa como la ruta de acceso base para la [raíz web](xref:fundamentals/index#web-root). Si no existe la ruta de acceso de la raíz web, el host no se puede iniciar.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Para obtener más información, consulte:

* [Aspectos básicos: Raíz del contenido](xref:fundamentals/index#content-root)
* [Raíz web](#web-root)

### <a name="detailed-errors"></a>Errores detallados

Determina si se deben capturar los errores detallados.

**Clave**: detailedErrors  
**Tipo**: *bool* (`true` o `1`)  
**Valor predeterminado**: false  
**Establecer mediante**: `UseSetting`  
**Variable de entorno**: `ASPNETCORE_DETAILEDERRORS`

Cuando se habilita (o cuando el <a href="#environment">entorno</a> está establecido en `Development`), la aplicación captura excepciones detalladas.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Entorno

Establece el entorno de la aplicación.

**Clave**: environment  
**Tipo**: *cadena*  
**Predeterminado**: Producción  
**Establecer mediante**: `UseEnvironment`  
**Variable de entorno**: `ASPNETCORE_ENVIRONMENT`

El entorno se puede establecer en cualquier valor. Los valores definidos por el marco son `Development`, `Staging` y `Production`. Los valores no distinguen mayúsculas de minúsculas. De forma predeterminada, el *entorno* se lee desde la variable de entorno `ASPNETCORE_ENVIRONMENT`. Cuando se usa [Visual Studio](https://visualstudio.microsoft.com), las variables de entorno se pueden establecer en el archivo *launchSettings.json*. Para obtener más información, vea <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Ensamblados de inicio de hospedaje

Establece los ensamblados de inicio de hospedaje de la aplicación.

**Clave**: hostingStartupAssemblies  
**Tipo**: *cadena*  
**Predeterminado**: Cadena vacía  
**Establecer mediante**: `UseSetting`  
**Variable de entorno**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.

Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación. Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>Puerto HTTPS

Establezca puerto de redirección HTTPS. Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).

**Clave**: https_port **Tipo**: *cadena*
**Valor predeterminado**: no se ha establecido ningún valor predeterminado.
**Establecer mediante**: `UseSetting`
**Variable de entorno**: `ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Ensamblados de exclusión de inicio de hospedaje

Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.

**Clave**: hostingStartupExcludeAssemblies  
**Tipo**: *cadena*  
**Predeterminado**: Cadena vacía  
**Establecer mediante**: `UseSetting`  
**Variable de entorno**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferir las direcciones URL de hospedaje

Indica si el host debe escuchar en las direcciones URL configuradas con `WebHostBuilder` en lugar de las que estén configuradas con la implementación de `IServer`.

**Clave**: preferHostingUrls  
**Tipo**: *bool* (`true` o `1`)  
**Valor predeterminado**: true  
**Establecer mediante**: `PreferHostingUrls`  
**Variable de entorno**: `ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Evitar el inicio de hospedaje

Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación. Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.

**Clave**: preventHostingStartup  
**Tipo**: *bool* (`true` o `1`)  
**Valor predeterminado**: false  
**Establecer mediante**: `UseSetting`  
**Variable de entorno**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Direcciones URL de servidor

Indica las direcciones IP o las direcciones de host con los puertos y protocolos en que el servidor debe escuchar las solicitudes.

**Clave**: urls  
**Tipo**: *cadena*  
**Predeterminado**: http://localhost:5000  
**Establecer mediante**: `UseUrls`  
**Variable de entorno**: `ASPNETCORE_URLS`

Se establece una lista de prefijos de URL separados por punto y coma (;) a los que debe responder el servidor. Por ejemplo: `http://localhost:123`. Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`). El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL. Los formatos admitidos varían de un servidor a otro.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
Kestrel tiene su propia API de configuración de punto de conexión. Para obtener más información, vea <xref:fundamentals/servers/kestrel/endpoints>.
::: moniker-end
::: moniker range="< aspnetcore-5.0"
Kestrel tiene su propia API de configuración de punto de conexión. Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.
::: moniker-end

### <a name="shutdown-timeout"></a>Tiempo de espera de apagado

Especifica la cantidad de tiempo que se espera hasta el cierre del host web.

**Clave**: shutdownTimeoutSeconds  
**Tipo**: *int*  
**Predeterminado**: 5  
**Establecer mediante**: `UseShutdownTimeout`  
**Variable de entorno**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Aunque la clave acepta un *int* con `UseSetting` (por ejemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), el método de extensión [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) toma [TimeSpan](/dotnet/api/system.timespan).

Durante el período de tiempo de espera, el hospedaje:

* Activa [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Trata de detener los servicios hospedados y registra cualquier error que se produzca en los servicios que no se puedan detener.

Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación. Los servicios se detienen aun cuando no hayan terminado de procesarse. Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Ensamblado de inicio

Determina el ensamblado en el que se va a buscar la clase `Startup`.

**Clave**: startupAssembly  
**Tipo**: *cadena*  
**Predeterminado**: el ensamblado de la aplicación  
**Establecer mediante**: `UseStartup`  
**Variable de entorno**: `ASPNETCORE_STARTUPASSEMBLY`

Se puede hacer referencia al ensamblado por su nombre (`string`) o su tipo (`TStartup`). Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Raíz web

Establece la ruta de acceso relativa a los recursos estáticos de la aplicación.

**Clave**: webroot  
**Tipo**: *cadena*  
**Predeterminado**: De manera predeterminada, es `wwwroot`. Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*. Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.  
**Establecer mediante**: `UseWebRoot`  
**Variable de entorno**: `ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Para obtener más información, consulte:

* [Aspectos básicos: Raíz web](xref:fundamentals/index#web-root)
* [Raíz del contenido](#content-root)

## <a name="override-configuration"></a>Invalidación de la configuración

Use [Configuración](xref:fundamentals/configuration/index) para configurar el host web. En el ejemplo siguiente, la configuración del host se especifica de forma opcional en un archivo *hostsettings.json*. Cualquier configuración que se cargue del archivo *hostsettings.json* se puede reemplazar por argumentos de línea de comandos. La configuración generada (en `config`) se usa para configurar el host con [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). La configuración de `IWebHostBuilder` se agrega a la de la aplicación, pero lo contrario no es aplicable: `ConfigureAppConfiguration` no afecta a la configuración de `IWebHostBuilder`.

Reemplazar primero la configuración proporcionada por `UseUrls` por la de *hostsettings.json* y, luego, por la del argumento de línea de comandos:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) solo copia las claves del elemento `IConfiguration` proporcionado a la configuración del generador de host. Por consiguiente, el hecho de configurar `reloadOnChange: true` para los archivos de configuración XML, JSON e INI no tiene ningún efecto.

Para especificar el host que se ejecuta en una dirección URL determinada, se puede pasar el valor deseado desde un símbolo del sistema al ejecutar [dotnet run](/dotnet/core/tools/dotnet-run). El argumento de línea de comandos reemplaza el valor `urls` del archivo *hostsettings.json*, y el servidor efectúa la escucha en el puerto 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Administración del host

**Run**

El método `Run` inicia la aplicación web y bloquea el subproceso que realiza la llamada hasta que se apague el host:

```csharp
host.Run();
```

**Iniciar**

Ejecute el host de manera que se evite un bloqueo mediante una llamada a su método `Start`:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Si se pasa una lista de direcciones URL al método `Start`, la escucha se produce en las direcciones URL especificadas:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

La aplicación puede inicializar un nuevo host usando los valores preconfigurados de `CreateDefaultBuilder` mediante un método práctico estático. Estos métodos inician el servidor sin la salida de la consola y con [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) esperando una interrupción (Ctrl-C/SIGINT o SIGTERM):

**Start(RequestDelegate app)**

Start con `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!" `WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM). La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.

**Start(url de cadena, RequestDelegate app)**

Start con una dirección URL y `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Produce el mismo resultado que **Start(RequestDelegate app)** , excepto que la aplicación responde en `http://localhost:8080`.

**Start(acción\<IRouteBuilder> routeBuilder)**

Use una instancia de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar el middleware de enrutamiento:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Utilice las siguientes solicitudes de explorador con el ejemplo:

| Request                                    | Respuesta                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Produce una excepción con la cadena "ooops!" |
| `http://localhost:5000/throw`              | Produce una excepción con la cadena "Uh oh!" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM). La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.

**Start(url de cadena, acción\<IRouteBuilder> routeBuilder)**

Use una dirección URL y una instancia de `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Produce el mismo resultado que **Start(acción\<IRouteBuilder> routeBuilder)** , excepto que la aplicación responde en `http://localhost:8080`.

**StartWith(acción\<IApplicationBuilder> app)**

Proporciona un delegado para configurar `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!" `WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM). La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.

**StartWith(url de cadena, acción\<IApplicationBuilder> app)**

Proporcione una dirección URL y un delegado para configurar `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Produce el mismo resultado que **StartWith(acción\<IApplicationBuilder> app)** , excepto que la aplicación responde en `http://localhost:8080`.

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>Interfaz IWebHostEnvironment

La interfaz `IWebHostEnvironment` proporciona información sobre el entorno de hospedaje web de la aplicación. Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IWebHostEnvironment` a fin de utilizar sus propiedades y métodos de extensión:

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno. Como alternativa, inserte `IWebHostEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Además del método de extensión `IsDevelopment`, `IWebHostEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`. Para obtener más información, vea <xref:fundamentals/environments>.

El servicio `IWebHostEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IWebHostEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>Interfaz IHostingEnvironment

La [interfaz IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) proporciona información sobre el entorno de hospedaje web de la aplicación. Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IHostingEnvironment` a fin de utilizar sus propiedades y métodos de extensión:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno. Como alternativa, inserte `IHostingEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Además del método de extensión `IsDevelopment`, `IHostingEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`. Para obtener más información, vea <xref:fundamentals/environments>.

El servicio `IHostingEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>Interfaz IHostApplicationLifetime

`IHostApplicationLifetime` permite actividades posteriores al inicio y apagado. Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.

| Token de cancelación    | Se desencadena cuando&#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | El host se ha iniciado completamente. |
| `ApplicationStopped`  | El host está completando un apagado estable. Deben procesarse todas las solicitudes. El apagado se bloquea hasta que se complete este evento. |
| `ApplicationStopping` | El host está realizando un apagado estable. Puede que todavía se estén procesando las solicitudes. El apagado se bloquea hasta que se complete este evento. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

`StopApplication` solicita la terminación de la aplicación. La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>Interfaz IApplicationLifetime

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite actividades posteriores al inicio y apagado. Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.

| Token de cancelación    | Se desencadena cuando&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | El host se ha iniciado completamente. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | El host está completando un apagado estable. Deben procesarse todas las solicitudes. El apagado se bloquea hasta que se complete este evento. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | El host está realizando un apagado estable. Puede que todavía se estén procesando las solicitudes. El apagado se bloquea hasta que se complete este evento. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita la terminación de la aplicación. La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a>Validación del ámbito

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo.

Cuando `ValidateScopes` está establecido en `true`, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:

* Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.
* Los servicios con ámbito no se insertan directa o indirectamente en singletons.

El proveedor de servicios raíz se crea cuando se llama a [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider). La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.

De la eliminación de los servicios con ámbito se encarga el contenedor que los creó. Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran. Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.

Para validar los ámbitos siempre, incluso en el entorno de producción, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) con [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) en el generador de hosts:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
