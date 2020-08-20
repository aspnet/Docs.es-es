---
title: Proveedores de archivo en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 30c28e7bd4cd9c926b157f5a7b9e6688bd5b9b9a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634610"
---
# <a name="file-providers-in-aspnet-core"></a>Proveedores de archivo en ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos. Los proveedores de archivos se usan en el marco de ASP.NET Core. Por ejemplo:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.
* El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.
* [Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.
* Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de proveedor de archivos

La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expone métodos para:

* Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)
* Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Se puede leer en el archivo mediante el método <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.

La aplicación de ejemplo *FileProviderSample* muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementaciones del proveedor de archivos

En la tabla siguiente se enumeran las implementaciones de `IFileProvider`.

| Implementación | Descripción |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Se usa para proporcionar acceso combinado a archivos y directorios de uno o más proveedores. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Se usa para tener acceso a archivos insertados en ensamblados. |
| [PhysicalFileProvider](#physicalfileprovider) | Se usa para tener acceso a los archivos físicos del sistema. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico. `PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios. Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios. El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).

Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso absoluta al directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor. Los patrones globales no se admiten en la ruta de acceso al directorio.

El código siguiente muestra cómo usar `PhysicalFileProvider` para obtener el contenido del directorio y la información del archivo:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Tipos del ejemplo anterior:

* `provider` es `IFileProvider`.
* `contents` es `IDirectoryContents`.
* `fileInfo` es `IFileInfo`.

El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo. No se pueden pasar patrones globales al método `GetFileInfo`. El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.

La aplicación de ejemplo *FileProviderSample* crea el proveedor en el método `Startup.ConfigureServices` con <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados. `ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.

Para generar un manifiesto de los archivos incrustados:

1. Agregue el paquete de NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al proyecto.
1. Establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`. Especifique los archivos que desea incrustar con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.

La aplicación de ejemplo *FileProviderSample* crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Las sobrecargas adicionales le permiten:

* Especificar una ruta de acceso de archivo relativa.
* Definir el ámbito de archivos a la fecha de la última modificación.
* Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.

| Sobrecarga | Descripción |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Acepta parámetro de ruta de acceso relativa `root` opcional. Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>). La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`. `manifestName` representa el nombre del recurso incrustado que contiene el manifiesto. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores. Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.

En la aplicación de ejemplo *FileProviderSample*, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación. El código siguiente se encuentra en el método `Startup.ConfigureServices` del proyecto:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Observación de cambios

El método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> proporciona un escenario para ver uno o más archivos o directorios para detectar cambios. El método `Watch` realiza las acciones siguientes:

* Acepta una cadena de ruta de acceso a archivo, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.
* Devuelve un valor <xref:Microsoft.Extensions.Primitives.IChangeToken>.

El token de cambio resultante expone:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada. Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio. Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.

La aplicación de ejemplo *WatchConsole* escribe un mensaje cada vez que se modifica un archivo *.txt* en el directorio *TextFiles*:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable. Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).

### <a name="glob-patterns"></a>Patrones globales

Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*. Especifique grupos de archivos con estos patrones. Los dos caracteres comodín son `*` y `**`:

**`*`**  
Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo. Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.

**`**`**  
Coincide con cualquier elemento de varios niveles de directorios. Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.

En la tabla siguiente se proporcionan ejemplos comunes de patrones globales.

|Modelo  |Descripción  |
|---------|---------|
|`directory/file.txt`|Coincide con un archivo concreto en un directorio específico.|
|`directory/*.txt`|Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.|
|`directory/*/appsettings.json`|Coincide con todos los archivos *appsettings.json* que están en directorios exactamente un nivel por debajo de la carpeta *directorio*.|
|`directory/**/*.txt`|Coincide con todos los archivos con una extensión *.txt* y que se encuentran en cualquier lugar de la carpeta *directorio*.|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos. Los proveedores de archivos se usan en el marco de ASP.NET Core:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.
* El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.
* [Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.
* Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de proveedor de archivos

La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expone métodos para:

* Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)
* Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Puede leer del archivo mediante el método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).

La aplicación de ejemplo muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementaciones del proveedor de archivos

Hay tres implementaciones de `IFileProvider` disponibles.

| Implementación | Descripción |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | El proveedor físico se utiliza para acceder a los archivos físicos del sistema. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | El proveedor insertado de manifiestos se utiliza para tener acceder a archivos insertados en ensamblados. |
| [CompositeFileProvider](#compositefileprovider) | El proveedor compuesto se utiliza para proporcionar acceso combinado a archivos y directorios de uno o más proveedores. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico. `PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios. Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios. El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).

Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso del directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.

El código siguiente muestra cómo crear `PhysicalFileProvider` y usarlo para obtener el contenido del directorio y la información del archivo:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Tipos del ejemplo anterior:

* `provider` es `IFileProvider`.
* `contents` es `IDirectoryContents`.
* `fileInfo` es `IFileInfo`.

El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo. El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.

La aplicación de ejemplo crea el proveedor en la clase `Startup.ConfigureServices` de la aplicación mediante [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados. `ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.

Para generar un manifiesto de los archivos incrustados, establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`. Especifique los archivos que desea incrustar con [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.

La aplicación de ejemplo crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Las sobrecargas adicionales le permiten:

* Especificar una ruta de acceso de archivo relativa.
* Definir el ámbito de archivos a la fecha de la última modificación.
* Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.

| Sobrecarga | Descripción |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Acepta parámetro de ruta de acceso relativa `root` opcional. Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>). La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`. `manifestName` representa el nombre del recurso incrustado que contiene el manifiesto. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores. Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.

En la aplicación de ejemplo, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Observación de cambios

El método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona un escenario para ver uno o más archivos o directorios para detectar cambios. `Watch` acepta una cadena de ruta de acceso, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos. `Watch` devuelve un valor de <xref:Microsoft.Extensions.Primitives.IChangeToken>. El token de cambio expone:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada. Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio. Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.

En la aplicación de ejemplo, la aplicación de consola *WatchConsole* se configura para mostrar un mensaje cada vez que se modifica un archivo de texto:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable. Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).

## <a name="glob-patterns"></a>Patrones globales

Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*. Especifique grupos de archivos con estos patrones. Los dos caracteres comodín son `*` y `**`:

**`*`**  
Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo. Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.

**`**`**  
Coincide con cualquier elemento de varios niveles de directorios. Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.

**Ejemplos de patrones globales**

**`directory/file.txt`**  
Coincide con un archivo concreto en un directorio específico.

**`directory/*.txt`**  
Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.

**`directory/*/appsettings.json`**  
Coincide con todos los archivos `appsettings.json` que estén en directorios exactamente un nivel por debajo de la carpeta *directorio*.

**`directory/**/*.txt`**  
Coincide con todos los archivos que tengan la extensión *.txt* y se encuentren en cualquier lugar de la carpeta *directorio*.

::: moniker-end
