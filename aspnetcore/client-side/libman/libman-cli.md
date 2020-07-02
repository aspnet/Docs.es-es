---
title: Uso de la CLI de LibMan con ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo usar la CLI de LibMan en un proyecto de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: ed5dffb83a2f1a40f3d6596d23135c0fa5b6791f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403188"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Uso de la CLI de LibMan con ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie)

La CLI de [LibMan](xref:client-side/libman/index) es una herramienta multiplataforma con la misma compatibilidad que .NET Core.

## <a name="prerequisites"></a>Requisitos previos

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalación

Para instalar la CLI de LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Se instala una [herramienta global de .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) desde el paquete NuGet [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/).

Para instalar la CLI de LibMan desde un origen de paquetes NuGet específico:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

En el ejemplo anterior, se instala una herramienta global de .NET Core desde el archivo *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* del equipo Windows.

## <a name="usage"></a>Uso

Tras la correcta instalación de la CLI, se puede usar el siguiente comando:

```console
libman
```

Para ver la versión instalada de la CLI:

```console
libman --version
```

Para ver los comandos disponibles de la CLI:

```console
libman --help
```

El comando anterior muestra una salida similar a la siguiente:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

En las secciones siguientes se describen los comandos disponibles de la CLI.

## <a name="initialize-libman-in-the-project"></a>Inicialización de LibMan en el proyecto

El comando `libman init` crea un archivo *libman.json* si no existe ninguno. El archivo se crea con el contenido de la plantilla de elemento predeterminado.

### <a name="synopsis"></a>Sinopsis

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman init`:

* `-d|--default-destination <PATH>`

  Ruta de acceso relativa a la carpeta actual. Los archivos de biblioteca se instalan en esta ubicación si no se ha definido ninguna propiedad `destination` de una biblioteca en *libman.json*. El valor `<PATH>` se escribe en la propiedad `defaultDestination` de *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Proveedor que se va a usar si no hay ninguno definido en una biblioteca dada. El valor `<PROVIDER>` se escribe en la propiedad `defaultProvider` de *libman.json*. Reemplace `<PROVIDER>` con uno de los siguientes valores:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

Para crear un archivo *libman.json* en un proyecto de ASP.NET Core:

* Vaya a la raíz del proyecto.
* Ejecute el siguiente comando:

  ```console
  libman init
  ```

* Escriba el nombre del proveedor predeterminado o presione `Enter` para usar el proveedor CDNJS predeterminado. Los valores válidos son:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Comando libman init: proveedor predeterminado](_static/libman-init-provider.png)

Se agrega un archivo *libman.json* a la raíz del proyecto con el siguiente contenido:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Adición de archivos de biblioteca

El comando `libman install` descarga e instala los archivos de biblioteca en el proyecto. Se agrega un archivo *libman.json* si no existe ninguno. El archivo *libman.json* se modifica para almacenar los detalles de configuración de los archivos de biblioteca.

### <a name="synopsis"></a>Sinopsis

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

Nombre de la biblioteca que se va a instalar. Este nombre puede incluir la notación del número de versión (por ejemplo, `@1.2.0`).

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman install`:

* `-d|--destination <PATH>`

  Ubicación en la que se va a instalar la biblioteca. Si no se especifica, se usa la ubicación predeterminada. Si no se especifica ninguna propiedad `defaultDestination` en *libman.json*, esta opción es obligatoria.

* `--files <FILE>`

  Especifique el nombre del archivo de la biblioteca que se va a instalar. Si no se especifica, se instalan todos los archivos de la biblioteca. Proporcione una opción `--files` por cada archivo que se va a instalar. También se admiten las rutas de acceso relativas. Por ejemplo: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nombre del proveedor que se va a usar en la adquisición de la biblioteca. Reemplace `<PROVIDER>` con uno de los siguientes valores:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Si no se especifica, se usa la propiedad `defaultProvider` de *libman.json*. Si no se especifica ninguna propiedad `defaultProvider` en *libman.json*, esta opción es obligatoria.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

Fíjese en el siguiente archivo *libman.json*:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Para instalar el archivo *jquery.min.js* de la versión 3.2.1 de jQuery en la carpeta *wwwroot/scripts/jquery* mediante el proveedor CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

El archivo *libman.json* es similar al siguiente:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Para instalar los archivos *calendar.js* y *calendar.css* desde *C:\\temp\\contosoCalendar\\* mediante el proveedor del sistema de archivos:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

El siguiente aviso aparece por dos motivos:

* El archivo *libman.json* no contiene una propiedad `defaultDestination`.
* El comando `libman install` no contiene la opción `-d|--destination`.

![Comando libman install: destino](_static/libman-install-destination.png)

Después de aceptar el destino predeterminado, el archivo *libman.json* es similar al siguiente:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Restauración de los archivos de biblioteca

El comando `libman restore` instala los archivos de biblioteca definidos en *libman.json*. Se aplican las siguientes reglas:

* Si no existe ningún archivo *libman.json* en la raíz del proyecto, se devuelve un error.
* Si una biblioteca especifica un proveedor, se omite la propiedad `defaultProvider` de *libman.json*.
* Si una biblioteca especifica un destino, se omite la propiedad `defaultDestination` de *libman.json*.

### <a name="synopsis"></a>Sinopsis

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman restore`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

Para restaurar los archivos de biblioteca definidos en *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Eliminación de archivos de biblioteca

El comando `libman clean` elimina los archivos de biblioteca restaurados previamente mediante LibMan. Se eliminan todas las carpetas que queden vacías después de esta operación. No se eliminan las configuraciones asociadas de los archivos de biblioteca de la propiedad `libraries` de *libman.json*.

### <a name="synopsis"></a>Sinopsis

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman clean`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

Para eliminar archivos de biblioteca instalados mediante LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Desinstalación de archivos de biblioteca

El comando `libman uninstall`:

* Elimina todos los archivos asociados a la biblioteca especificada del destino en *libman.json*.
* Quita la configuración de biblioteca asociada de *libman.json*.

Se produce un error si:

* No existe ningún archivo *libman.json* en la raíz del proyecto.
* La biblioteca especificada no existe.

Si hay más de una biblioteca con el mismo nombre instalada, se le pedirá que elija una.

### <a name="synopsis"></a>Sinopsis

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

Nombre de la biblioteca que se va a desinstalar. Este nombre puede incluir la notación del número de versión (por ejemplo, `@1.2.0`).

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman uninstall`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

Fíjese en el siguiente archivo *libman.json*:

[!code-json[](samples/LibManSample/libman.json)]

* Para desinstalar jQuery, puede usar cualquiera de los siguientes comandos:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Para desinstalar los archivos Lodash instalados mediante el proveedor `filesystem`:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Actualización de la versión de la biblioteca

El comando `libman update` actualiza una biblioteca instalada mediante LibMan a la versión especificada.

Se produce un error si:

* No existe ningún archivo *libman.json* en la raíz del proyecto.
* La biblioteca especificada no existe.

Si hay más de una biblioteca con el mismo nombre instalada, se le pedirá que elija una.

### <a name="synopsis"></a>Sinopsis

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

Nombre de la biblioteca que se va a actualizar.

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman update`:

* `-pre`

  Obtenga la versión preliminar más reciente de la biblioteca.

* `--to <VERSION>`

  Obtenga una versión específica de la biblioteca.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

* Para actualizar jQuery a la versión más reciente:

  ```console
  libman update jquery
  ```

* Para actualizar JQuery a la versión 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Para actualizar jQuery a la versión preliminar más reciente:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Administración de la memoria caché de la biblioteca

El comando `libman cache` administra la memoria caché de la biblioteca LibMan. El proveedor `filesystem` no usa la memoria caché de la biblioteca.

### <a name="synopsis"></a>Sinopsis

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumentos

`PROVIDER`

Solo se usa con el comando `clean`. Especifica la memoria caché del proveedor que se va a limpiar. Los valores válidos son:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opciones

Las siguientes opciones están disponibles para el comando `libman cache`:

* `--files`

  Enumera los nombres de los archivos que se almacenan en la memoria caché.

* `--libraries`

  Enumera los nombres de las bibliotecas que se almacenan en la memoria caché.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Ejemplos

* Para ver los nombres de las bibliotecas almacenadas en la memoria caché por proveedor, use uno de los siguientes comandos:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Esto genera una salida similar a la siguiente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Para ver los nombres de los archivos de biblioteca almacenados en la memoria caché por proveedor:

  ```console
  libman cache list --files
  ```

  Esto genera una salida similar a la siguiente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Observe que la salida anterior muestra que las versiones 3.2.1 y 3.3.1 de jQuery están almacenadas en la memoria caché en el proveedor CDNJS.

* Para vaciar la memoria caché de la biblioteca del proveedor CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Después de vaciar la memoria caché del proveedor CDNJS, el comando `libman cache list` muestra lo siguiente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Para vaciar la memoria caché de todos los proveedores admitidos:

  ```console
  libman cache clean
  ```

  Después de vaciar la memoria caché de todos los proveedores, el comando `libman cache list` muestra lo siguiente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Recursos adicionales

* [Instalación de una herramienta global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repositorio de LibMan en GitHub](https://github.com/aspnet/LibraryManager)
