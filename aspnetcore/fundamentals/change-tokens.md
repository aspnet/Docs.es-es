---
title: Detección de cambios con tokens de cambio en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar tokens de cambio para realizar el seguimiento de los cambios.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053779"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Detección de cambios con tokens de cambio en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interfaz IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio. `IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.

`IChangeToken` tiene dos propiedades:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva. Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios. También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.

La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado. `HasChanged` se debe establecer antes de que se invoque la devolución de llamada.

## <a name="changetoken-class"></a>Clase ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio. `ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.

El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:

* `Func<IChangeToken>` genera el token.
* Se llama a `Action` cuando cambia el token.

La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.

`OnChange` devuelve un valor de <xref:System.IDisposable>. Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Ejemplos de uso de tokens de cambio en ASP.NET Core

Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:

* Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.
* Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.
* Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.

## <a name="monitor-for-configuration-changes"></a>Supervisión de los cambios de configuración

De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.

Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`. `reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo. Esta configuración aparece en el método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.

En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración. Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.

El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración. Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo. El ejemplo usa el algoritmo hash seguro 1. Se implementa un reintento con una interrupción exponencial. El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Token de cambio de inicio simple

Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.

En `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` proporciona el token. La devolución de llamada es el método `InvokeChanged`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

El `state` de la devolución de llamada se usa para pasar el `IWebHostEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo). Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.

Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.

### <a name="monitor-configuration-changes-as-a-service"></a>Supervisión de los cambios de configuración como servicio

En el ejemplo se implementa lo siguiente:

* La supervisión del token de inicio básico.
* La supervisión como servicio.
* Un mecanismo para habilitar y deshabilitar la supervisión.

El ejemplo establece una interfaz `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` proporciona el token. `InvokeChanged` es el método de devolución de llamada. El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión. Se usan dos propiedades:

* `MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.
* `CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.

El método `InvokeChanged` es similar al enfoque anterior, excepto en que:

* No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.
* Genera el `state` actual en su salida de `WriteConsole`.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

En la página Index se ofrece al usuario el control sobre la supervisión de la configuración. La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.

*Páginas/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual. Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.

Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.

*Páginas/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Supervisión de los cambios de archivos en caché

El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria). Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.

Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos. En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché. Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.

El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché. En la aplicación de ejemplo se muestra una implementación del enfoque.

En el ejemplo se usa `GetFileContent` para:

* Devolver el contenido del archivo.
* Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Se crea un `FileService` para administrar las búsquedas de archivos en caché. La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).

Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:

1. El contenido del archivo se obtiene mediante `GetFileContent`.
1. Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). La devolución de llamada del token se desencadena cuando se modifica el archivo.
1. El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.

En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación. `IWebHostEnvironment.ContentRootFileProvider` se usa para obtener un <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunte a `IWebHostEnvironment.ContentRootPath` de la aplicación. La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.

En `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

El modelo de página carga el contenido del archivo mediante el servicio.

En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Clase CompositeChangeToken

Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`. En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`. Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interfaz IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio. `IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

`IChangeToken` tiene dos propiedades:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva. Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios. También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.

La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado. `HasChanged` se debe establecer antes de que se invoque la devolución de llamada.

## <a name="changetoken-class"></a>Clase ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio. `ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:

* `Func<IChangeToken>` genera el token.
* Se llama a `Action` cuando cambia el token.

La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.

`OnChange` devuelve un valor de <xref:System.IDisposable>. Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Ejemplos de uso de tokens de cambio en ASP.NET Core

Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:

* Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.
* Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.
* Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.

## <a name="monitor-for-configuration-changes"></a>Supervisión de los cambios de configuración

De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.

Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`. `reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo. Esta configuración aparece en el método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.

En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración. Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.

El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración. Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo. El ejemplo usa el algoritmo hash seguro 1. Se implementa un reintento con una interrupción exponencial. El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Token de cambio de inicio simple

Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.

En `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` proporciona el token. La devolución de llamada es el método `InvokeChanged`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

El `state` de la devolución de llamada se usa para pasar el `IHostingEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo). Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.

Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.

### <a name="monitor-configuration-changes-as-a-service"></a>Supervisión de los cambios de configuración como servicio

En el ejemplo se implementa lo siguiente:

* La supervisión del token de inicio básico.
* La supervisión como servicio.
* Un mecanismo para habilitar y deshabilitar la supervisión.

El ejemplo establece una interfaz `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` proporciona el token. `InvokeChanged` es el método de devolución de llamada. El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión. Se usan dos propiedades:

* `MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.
* `CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.

El método `InvokeChanged` es similar al enfoque anterior, excepto en que:

* No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.
* Genera el `state` actual en su salida de `WriteConsole`.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

En la página Index se ofrece al usuario el control sobre la supervisión de la configuración. La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.

*Páginas/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual. Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.

Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.

*Páginas/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Supervisión de los cambios de archivos en caché

El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria). Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.

Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos. En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché. Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.

El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché. En la aplicación de ejemplo se muestra una implementación del enfoque.

En el ejemplo se usa `GetFileContent` para:

* Devolver el contenido del archivo.
* Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Se crea un `FileService` para administrar las búsquedas de archivos en caché. La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).

Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:

1. El contenido del archivo se obtiene mediante `GetFileContent`.
1. Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). La devolución de llamada del token se desencadena cuando se modifica el archivo.
1. El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.

En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se usa para obtener un elemento <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunta a la <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de la aplicación. La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.

En `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

El modelo de página carga el contenido del archivo mediante el servicio.

En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Clase CompositeChangeToken

Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`. En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`. Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
