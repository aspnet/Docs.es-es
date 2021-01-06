---
title: Uso de la CLI de LibMan con ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo usar la CLI de LibMan en un proyecto de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054754"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="f2d64-103">Uso de la CLI de LibMan con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2d64-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="f2d64-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="f2d64-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="f2d64-105">La CLI de [LibMan](xref:client-side/libman/index) es una herramienta multiplataforma con la misma compatibilidad que .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2d64-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f2d64-106">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="f2d64-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="f2d64-107">Instalación</span><span class="sxs-lookup"><span data-stu-id="f2d64-107">Installation</span></span>

<span data-ttu-id="f2d64-108">Para instalar la CLI de LibMan:</span><span class="sxs-lookup"><span data-stu-id="f2d64-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="f2d64-109">Se instala una [herramienta global de .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) desde el paquete NuGet [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/).</span><span class="sxs-lookup"><span data-stu-id="f2d64-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="f2d64-110">Para instalar la CLI de LibMan desde un origen de paquetes NuGet específico:</span><span class="sxs-lookup"><span data-stu-id="f2d64-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="f2d64-111">En el ejemplo anterior, se instala una herramienta global de .NET Core desde el archivo *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* del equipo Windows.</span><span class="sxs-lookup"><span data-stu-id="f2d64-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="f2d64-112">Uso</span><span class="sxs-lookup"><span data-stu-id="f2d64-112">Usage</span></span>

<span data-ttu-id="f2d64-113">Tras la correcta instalación de la CLI, se puede usar el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f2d64-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="f2d64-114">Para ver la versión instalada de la CLI:</span><span class="sxs-lookup"><span data-stu-id="f2d64-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="f2d64-115">Para ver los comandos disponibles de la CLI:</span><span class="sxs-lookup"><span data-stu-id="f2d64-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="f2d64-116">El comando anterior muestra una salida similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="f2d64-117">En las secciones siguientes se describen los comandos disponibles de la CLI.</span><span class="sxs-lookup"><span data-stu-id="f2d64-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="f2d64-118">Inicialización de LibMan en el proyecto</span><span class="sxs-lookup"><span data-stu-id="f2d64-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="f2d64-119">El comando `libman init` crea un archivo *libman.json* si no existe ninguno.</span><span class="sxs-lookup"><span data-stu-id="f2d64-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="f2d64-120">El archivo se crea con el contenido de la plantilla de elemento predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f2d64-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-121">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="f2d64-122">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-122">Options</span></span>

<span data-ttu-id="f2d64-123">Las siguientes opciones están disponibles para el comando `libman init`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="f2d64-124">Ruta de acceso relativa a la carpeta actual.</span><span class="sxs-lookup"><span data-stu-id="f2d64-124">A path relative to the current folder.</span></span> <span data-ttu-id="f2d64-125">Los archivos de biblioteca se instalan en esta ubicación si no se ha definido ninguna propiedad `destination` de una biblioteca en *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="f2d64-126">El valor `<PATH>` se escribe en la propiedad `defaultDestination` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="f2d64-127">Proveedor que se va a usar si no hay ninguno definido en una biblioteca dada.</span><span class="sxs-lookup"><span data-stu-id="f2d64-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="f2d64-128">El valor `<PROVIDER>` se escribe en la propiedad `defaultProvider` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="f2d64-129">Reemplace `<PROVIDER>` con uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="f2d64-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-130">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-130">Examples</span></span>

<span data-ttu-id="f2d64-131">Para crear un archivo *libman.json* en un proyecto de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f2d64-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="f2d64-132">Vaya a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2d64-132">Navigate to the project root.</span></span>
* <span data-ttu-id="f2d64-133">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f2d64-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="f2d64-134">Escriba el nombre del proveedor predeterminado o presione `Enter` para usar el proveedor CDNJS predeterminado.</span><span class="sxs-lookup"><span data-stu-id="f2d64-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="f2d64-135">Los valores válidos son:</span><span class="sxs-lookup"><span data-stu-id="f2d64-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Comando libman init: proveedor predeterminado](_static/libman-init-provider.png)

<span data-ttu-id="f2d64-137">Se agrega un archivo *libman.json* a la raíz del proyecto con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="f2d64-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="f2d64-138">Adición de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-138">Add library files</span></span>

<span data-ttu-id="f2d64-139">El comando `libman install` descarga e instala los archivos de biblioteca en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2d64-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="f2d64-140">Se agrega un archivo *libman.json* si no existe ninguno.</span><span class="sxs-lookup"><span data-stu-id="f2d64-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="f2d64-141">El archivo *libman.json* se modifica para almacenar los detalles de configuración de los archivos de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-142">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="f2d64-143">Argumentos</span><span class="sxs-lookup"><span data-stu-id="f2d64-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="f2d64-144">Nombre de la biblioteca que se va a instalar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-144">The name of the library to install.</span></span> <span data-ttu-id="f2d64-145">Este nombre puede incluir la notación del número de versión (por ejemplo, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="f2d64-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="f2d64-146">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-146">Options</span></span>

<span data-ttu-id="f2d64-147">Las siguientes opciones están disponibles para el comando `libman install`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="f2d64-148">Ubicación en la que se va a instalar la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-148">The location to install the library.</span></span> <span data-ttu-id="f2d64-149">Si no se especifica, se usa la ubicación predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f2d64-149">If not specified, the default location is used.</span></span> <span data-ttu-id="f2d64-150">Si no se especifica ninguna propiedad `defaultDestination` en *libman.json*, esta opción es obligatoria.</span><span class="sxs-lookup"><span data-stu-id="f2d64-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="f2d64-151">Especifique el nombre del archivo de la biblioteca que se va a instalar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="f2d64-152">Si no se especifica, se instalan todos los archivos de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="f2d64-153">Proporcione una opción `--files` por cada archivo que se va a instalar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="f2d64-154">También se admiten las rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="f2d64-154">Relative paths are supported too.</span></span> <span data-ttu-id="f2d64-155">Por ejemplo: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="f2d64-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="f2d64-156">Nombre del proveedor que se va a usar en la adquisición de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="f2d64-157">Reemplace `<PROVIDER>` con uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="f2d64-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="f2d64-158">Si no se especifica, se usa la propiedad `defaultProvider` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="f2d64-159">Si no se especifica ninguna propiedad `defaultProvider` en *libman.json*, esta opción es obligatoria.</span><span class="sxs-lookup"><span data-stu-id="f2d64-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-160">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-160">Examples</span></span>

<span data-ttu-id="f2d64-161">Fíjese en el siguiente archivo *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="f2d64-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="f2d64-162">Para instalar el archivo *jquery.min.js* de la versión 3.2.1 de jQuery en la carpeta *wwwroot/scripts/jquery* mediante el proveedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="f2d64-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="f2d64-163">El archivo *libman.json* es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="f2d64-164">Para instalar los archivos *calendar.js* y *calendar.css* desde *C:\\temp\\contosoCalendar\\* mediante el proveedor del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="f2d64-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="f2d64-165">El siguiente aviso aparece por dos motivos:</span><span class="sxs-lookup"><span data-stu-id="f2d64-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="f2d64-166">El archivo *libman.json* no contiene una propiedad `defaultDestination`.</span><span class="sxs-lookup"><span data-stu-id="f2d64-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="f2d64-167">El comando `libman install` no contiene la opción `-d|--destination`.</span><span class="sxs-lookup"><span data-stu-id="f2d64-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Comando libman install: destino](_static/libman-install-destination.png)

<span data-ttu-id="f2d64-169">Después de aceptar el destino predeterminado, el archivo *libman.json* es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="f2d64-170">Restauración de los archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-170">Restore library files</span></span>

<span data-ttu-id="f2d64-171">El comando `libman restore` instala los archivos de biblioteca definidos en *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="f2d64-172">Se aplican las siguientes reglas:</span><span class="sxs-lookup"><span data-stu-id="f2d64-172">The following rules apply:</span></span>

* <span data-ttu-id="f2d64-173">Si no existe ningún archivo *libman.json* en la raíz del proyecto, se devuelve un error.</span><span class="sxs-lookup"><span data-stu-id="f2d64-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="f2d64-174">Si una biblioteca especifica un proveedor, se omite la propiedad `defaultProvider` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="f2d64-175">Si una biblioteca especifica un destino, se omite la propiedad `defaultDestination` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-176">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="f2d64-177">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-177">Options</span></span>

<span data-ttu-id="f2d64-178">Las siguientes opciones están disponibles para el comando `libman restore`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-179">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-179">Examples</span></span>

<span data-ttu-id="f2d64-180">Para restaurar los archivos de biblioteca definidos en *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="f2d64-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="f2d64-181">Eliminación de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-181">Delete library files</span></span>

<span data-ttu-id="f2d64-182">El comando `libman clean` elimina los archivos de biblioteca restaurados previamente mediante LibMan.</span><span class="sxs-lookup"><span data-stu-id="f2d64-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="f2d64-183">Se eliminan todas las carpetas que queden vacías después de esta operación.</span><span class="sxs-lookup"><span data-stu-id="f2d64-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="f2d64-184">No se eliminan las configuraciones asociadas de los archivos de biblioteca de la propiedad `libraries` de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-185">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="f2d64-186">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-186">Options</span></span>

<span data-ttu-id="f2d64-187">Las siguientes opciones están disponibles para el comando `libman clean`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-188">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-188">Examples</span></span>

<span data-ttu-id="f2d64-189">Para eliminar archivos de biblioteca instalados mediante LibMan:</span><span class="sxs-lookup"><span data-stu-id="f2d64-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="f2d64-190">Desinstalación de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-190">Uninstall library files</span></span>

<span data-ttu-id="f2d64-191">El comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="f2d64-192">Elimina todos los archivos asociados a la biblioteca especificada del destino en *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="f2d64-193">Quita la configuración de biblioteca asociada de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="f2d64-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="f2d64-194">Se produce un error si:</span><span class="sxs-lookup"><span data-stu-id="f2d64-194">An error occurs when:</span></span>

* <span data-ttu-id="f2d64-195">No existe ningún archivo *libman.json* en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2d64-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="f2d64-196">La biblioteca especificada no existe.</span><span class="sxs-lookup"><span data-stu-id="f2d64-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="f2d64-197">Si hay más de una biblioteca con el mismo nombre instalada, se le pedirá que elija una.</span><span class="sxs-lookup"><span data-stu-id="f2d64-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-198">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="f2d64-199">Argumentos</span><span class="sxs-lookup"><span data-stu-id="f2d64-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="f2d64-200">Nombre de la biblioteca que se va a desinstalar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-200">The name of the library to uninstall.</span></span> <span data-ttu-id="f2d64-201">Este nombre puede incluir la notación del número de versión (por ejemplo, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="f2d64-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="f2d64-202">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-202">Options</span></span>

<span data-ttu-id="f2d64-203">Las siguientes opciones están disponibles para el comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-204">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-204">Examples</span></span>

<span data-ttu-id="f2d64-205">Fíjese en el siguiente archivo *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="f2d64-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="f2d64-206">Para desinstalar jQuery, puede usar cualquiera de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="f2d64-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="f2d64-207">Para desinstalar los archivos Lodash instalados mediante el proveedor `filesystem`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="f2d64-208">Actualización de la versión de la biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-208">Update library version</span></span>

<span data-ttu-id="f2d64-209">El comando `libman update` actualiza una biblioteca instalada mediante LibMan a la versión especificada.</span><span class="sxs-lookup"><span data-stu-id="f2d64-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="f2d64-210">Se produce un error si:</span><span class="sxs-lookup"><span data-stu-id="f2d64-210">An error occurs when:</span></span>

* <span data-ttu-id="f2d64-211">No existe ningún archivo *libman.json* en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2d64-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="f2d64-212">La biblioteca especificada no existe.</span><span class="sxs-lookup"><span data-stu-id="f2d64-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="f2d64-213">Si hay más de una biblioteca con el mismo nombre instalada, se le pedirá que elija una.</span><span class="sxs-lookup"><span data-stu-id="f2d64-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-214">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="f2d64-215">Argumentos</span><span class="sxs-lookup"><span data-stu-id="f2d64-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="f2d64-216">Nombre de la biblioteca que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="f2d64-217">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-217">Options</span></span>

<span data-ttu-id="f2d64-218">Las siguientes opciones están disponibles para el comando `libman update`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="f2d64-219">Obtenga la versión preliminar más reciente de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="f2d64-220">Obtenga una versión específica de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-221">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-221">Examples</span></span>

* <span data-ttu-id="f2d64-222">Para actualizar jQuery a la versión más reciente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="f2d64-223">Para actualizar JQuery a la versión 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="f2d64-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="f2d64-224">Para actualizar jQuery a la versión preliminar más reciente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="f2d64-225">Administración de la memoria caché de la biblioteca</span><span class="sxs-lookup"><span data-stu-id="f2d64-225">Manage library cache</span></span>

<span data-ttu-id="f2d64-226">El comando `libman cache` administra la memoria caché de la biblioteca LibMan.</span><span class="sxs-lookup"><span data-stu-id="f2d64-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="f2d64-227">El proveedor `filesystem` no usa la memoria caché de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f2d64-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="f2d64-228">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="f2d64-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="f2d64-229">Argumentos</span><span class="sxs-lookup"><span data-stu-id="f2d64-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="f2d64-230">Solo se usa con el comando `clean`.</span><span class="sxs-lookup"><span data-stu-id="f2d64-230">Only used with the `clean` command.</span></span> <span data-ttu-id="f2d64-231">Especifica la memoria caché del proveedor que se va a limpiar.</span><span class="sxs-lookup"><span data-stu-id="f2d64-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="f2d64-232">Los valores válidos son:</span><span class="sxs-lookup"><span data-stu-id="f2d64-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="f2d64-233">Opciones</span><span class="sxs-lookup"><span data-stu-id="f2d64-233">Options</span></span>

<span data-ttu-id="f2d64-234">Las siguientes opciones están disponibles para el comando `libman cache`:</span><span class="sxs-lookup"><span data-stu-id="f2d64-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="f2d64-235">Enumera los nombres de los archivos que se almacenan en la memoria caché.</span><span class="sxs-lookup"><span data-stu-id="f2d64-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="f2d64-236">Enumera los nombres de las bibliotecas que se almacenan en la memoria caché.</span><span class="sxs-lookup"><span data-stu-id="f2d64-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="f2d64-237">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="f2d64-237">Examples</span></span>

* <span data-ttu-id="f2d64-238">Para ver los nombres de las bibliotecas almacenadas en la memoria caché por proveedor, use uno de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="f2d64-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="f2d64-239">Esto genera una salida similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="f2d64-240">Para ver los nombres de los archivos de biblioteca almacenados en la memoria caché por proveedor:</span><span class="sxs-lookup"><span data-stu-id="f2d64-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="f2d64-241">Esto genera una salida similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="f2d64-242">Observe que la salida anterior muestra que las versiones 3.2.1 y 3.3.1 de jQuery están almacenadas en la memoria caché en el proveedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="f2d64-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="f2d64-243">Para vaciar la memoria caché de la biblioteca del proveedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="f2d64-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="f2d64-244">Después de vaciar la memoria caché del proveedor CDNJS, el comando `libman cache list` muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="f2d64-245">Para vaciar la memoria caché de todos los proveedores admitidos:</span><span class="sxs-lookup"><span data-stu-id="f2d64-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="f2d64-246">Después de vaciar la memoria caché de todos los proveedores, el comando `libman cache list` muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2d64-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="f2d64-247">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f2d64-247">Additional resources</span></span>

* [<span data-ttu-id="f2d64-248">Instalación de una herramienta global</span><span class="sxs-lookup"><span data-stu-id="f2d64-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="f2d64-249">Repositorio de LibMan en GitHub</span><span class="sxs-lookup"><span data-stu-id="f2d64-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
