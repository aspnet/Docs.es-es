---
title: Proveedores de archivo en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 16e5ead9898125c804da4d60322510474201d897
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059447"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="4815b-103">Proveedores de archivo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4815b-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="4815b-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4815b-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4815b-105">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="4815b-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="4815b-106">Los proveedores de archivos se usan en el marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4815b-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="4815b-107">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4815b-107">For example:</span></span>

* <span data-ttu-id="4815b-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="4815b-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="4815b-109">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="4815b-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="4815b-110">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="4815b-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="4815b-111">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="4815b-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="4815b-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4815b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="4815b-113">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="4815b-113">File Provider interfaces</span></span>

<span data-ttu-id="4815b-114">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="4815b-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="4815b-115">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="4815b-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="4815b-116">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="4815b-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="4815b-117">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="4815b-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="4815b-118">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="4815b-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="4815b-119">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="4815b-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="4815b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="4815b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="4815b-121">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="4815b-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="4815b-122">Se puede leer en el archivo mediante el método <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="4815b-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="4815b-123">La aplicación de ejemplo *FileProviderSample* muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4815b-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="4815b-124">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="4815b-124">File Provider implementations</span></span>

<span data-ttu-id="4815b-125">En la tabla siguiente se enumeran las implementaciones de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="4815b-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="4815b-126">Implementación</span><span class="sxs-lookup"><span data-stu-id="4815b-126">Implementation</span></span> | <span data-ttu-id="4815b-127">Descripción</span><span class="sxs-lookup"><span data-stu-id="4815b-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="4815b-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="4815b-129">Se usa para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="4815b-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="4815b-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="4815b-131">Se usa para tener acceso a archivos insertados en ensamblados.</span><span class="sxs-lookup"><span data-stu-id="4815b-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="4815b-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="4815b-133">Se usa para tener acceso a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="4815b-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="4815b-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-134">PhysicalFileProvider</span></span>

<span data-ttu-id="4815b-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="4815b-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="4815b-136">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="4815b-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="4815b-137">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="4815b-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="4815b-138">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4815b-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="4815b-139">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso absoluta al directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="4815b-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="4815b-140">Los patrones globales no se admiten en la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="4815b-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="4815b-141">El código siguiente muestra cómo usar `PhysicalFileProvider` para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="4815b-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="4815b-142">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4815b-142">Types in the preceding example:</span></span>

* <span data-ttu-id="4815b-143">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="4815b-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="4815b-144">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="4815b-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="4815b-145">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="4815b-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="4815b-146">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="4815b-147">No se pueden pasar patrones globales al método `GetFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="4815b-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="4815b-148">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="4815b-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="4815b-149">La aplicación de ejemplo *FileProviderSample* crea el proveedor en el método `Startup.ConfigureServices` con <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="4815b-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="4815b-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="4815b-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="4815b-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="4815b-152">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="4815b-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="4815b-153">Para generar un manifiesto de los archivos incrustados:</span><span class="sxs-lookup"><span data-stu-id="4815b-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="4815b-154">Agregue el paquete de NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="4815b-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="4815b-155">Establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="4815b-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="4815b-156">Especifique los archivos que desea incrustar con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="4815b-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="4815b-157">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="4815b-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="4815b-158">La aplicación de ejemplo *FileProviderSample* crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="4815b-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="4815b-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4815b-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="4815b-160">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="4815b-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="4815b-161">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="4815b-161">Specify a relative file path.</span></span>
* <span data-ttu-id="4815b-162">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="4815b-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="4815b-163">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="4815b-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="4815b-164">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="4815b-164">Overload</span></span> | <span data-ttu-id="4815b-165">Descripción</span><span class="sxs-lookup"><span data-stu-id="4815b-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="4815b-166">Acepta parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="4815b-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="4815b-167">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="4815b-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="4815b-168">Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="4815b-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="4815b-169">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="4815b-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="4815b-170">Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="4815b-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="4815b-171">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="4815b-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="4815b-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-172">CompositeFileProvider</span></span>

<span data-ttu-id="4815b-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="4815b-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="4815b-174">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="4815b-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="4815b-175">En la aplicación de ejemplo *FileProviderSample*, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4815b-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="4815b-176">El código siguiente se encuentra en el método `Startup.ConfigureServices` del proyecto:</span><span class="sxs-lookup"><span data-stu-id="4815b-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="4815b-177">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="4815b-177">Watch for changes</span></span>

<span data-ttu-id="4815b-178">El método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="4815b-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="4815b-179">El método `Watch` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4815b-179">The `Watch` method:</span></span>

* <span data-ttu-id="4815b-180">Acepta una cadena de ruta de acceso a archivo, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="4815b-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="4815b-181">Devuelve un valor <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="4815b-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="4815b-182">El token de cambio resultante expone:</span><span class="sxs-lookup"><span data-stu-id="4815b-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="4815b-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="4815b-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="4815b-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="4815b-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="4815b-185">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="4815b-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="4815b-186">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="4815b-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="4815b-187">La aplicación de ejemplo *WatchConsole* escribe un mensaje cada vez que se modifica un archivo *.txt* en el directorio *TextFiles*:</span><span class="sxs-lookup"><span data-stu-id="4815b-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="4815b-188">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="4815b-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="4815b-189">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="4815b-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="4815b-190">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="4815b-190">Glob patterns</span></span>

<span data-ttu-id="4815b-191">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="4815b-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="4815b-192">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="4815b-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="4815b-193">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="4815b-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="4815b-194">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="4815b-195">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="4815b-196">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="4815b-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="4815b-197">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="4815b-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="4815b-198">En la tabla siguiente se proporcionan ejemplos comunes de patrones globales.</span><span class="sxs-lookup"><span data-stu-id="4815b-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="4815b-199">Modelo</span><span class="sxs-lookup"><span data-stu-id="4815b-199">Pattern</span></span>  |<span data-ttu-id="4815b-200">Descripción</span><span class="sxs-lookup"><span data-stu-id="4815b-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="4815b-201">Coincide con un archivo concreto en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="4815b-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="4815b-202">Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="4815b-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="4815b-203">Coincide con todos los archivos *appsettings.json* que estén en directorios exactamente un nivel por debajo de la carpeta *directory*.</span><span class="sxs-lookup"><span data-stu-id="4815b-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="4815b-204">Coincide con todos los archivos con una extensión *.txt* y que se encuentran en cualquier lugar de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="4815b-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4815b-205">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="4815b-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="4815b-206">Los proveedores de archivos se usan en el marco de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4815b-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="4815b-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="4815b-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="4815b-208">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="4815b-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="4815b-209">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="4815b-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="4815b-210">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="4815b-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="4815b-211">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4815b-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="4815b-212">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="4815b-212">File Provider interfaces</span></span>

<span data-ttu-id="4815b-213">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="4815b-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="4815b-214">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="4815b-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="4815b-215">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="4815b-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="4815b-216">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="4815b-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="4815b-217">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="4815b-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="4815b-218">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="4815b-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="4815b-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="4815b-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="4815b-220">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="4815b-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="4815b-221">Puede leer del archivo mediante el método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="4815b-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="4815b-222">La aplicación de ejemplo muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4815b-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="4815b-223">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="4815b-223">File Provider implementations</span></span>

<span data-ttu-id="4815b-224">Hay tres implementaciones de `IFileProvider` disponibles.</span><span class="sxs-lookup"><span data-stu-id="4815b-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="4815b-225">Implementación</span><span class="sxs-lookup"><span data-stu-id="4815b-225">Implementation</span></span> | <span data-ttu-id="4815b-226">Descripción</span><span class="sxs-lookup"><span data-stu-id="4815b-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="4815b-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="4815b-228">El proveedor físico se utiliza para acceder a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="4815b-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="4815b-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="4815b-230">El proveedor insertado de manifiestos se utiliza para tener acceder a archivos insertados en ensamblados.</span><span class="sxs-lookup"><span data-stu-id="4815b-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="4815b-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="4815b-232">El proveedor compuesto se utiliza para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="4815b-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="4815b-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-233">PhysicalFileProvider</span></span>

<span data-ttu-id="4815b-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="4815b-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="4815b-235">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="4815b-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="4815b-236">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="4815b-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="4815b-237">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4815b-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="4815b-238">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso del directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="4815b-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="4815b-239">El código siguiente muestra cómo crear `PhysicalFileProvider` y usarlo para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="4815b-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="4815b-240">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4815b-240">Types in the preceding example:</span></span>

* <span data-ttu-id="4815b-241">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="4815b-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="4815b-242">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="4815b-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="4815b-243">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="4815b-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="4815b-244">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="4815b-245">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="4815b-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="4815b-246">La aplicación de ejemplo crea el proveedor en la clase `Startup.ConfigureServices` de la aplicación mediante [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="4815b-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="4815b-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="4815b-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="4815b-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="4815b-249">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="4815b-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="4815b-250">Para generar un manifiesto de los archivos incrustados, establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="4815b-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="4815b-251">Especifique los archivos que desea incrustar con [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="4815b-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="4815b-252">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="4815b-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="4815b-253">La aplicación de ejemplo crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="4815b-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="4815b-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4815b-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="4815b-255">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="4815b-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="4815b-256">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="4815b-256">Specify a relative file path.</span></span>
* <span data-ttu-id="4815b-257">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="4815b-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="4815b-258">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="4815b-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="4815b-259">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="4815b-259">Overload</span></span> | <span data-ttu-id="4815b-260">Descripción</span><span class="sxs-lookup"><span data-stu-id="4815b-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="4815b-261">Acepta parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="4815b-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="4815b-262">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="4815b-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="4815b-263">Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="4815b-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="4815b-264">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="4815b-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="4815b-265">Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="4815b-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="4815b-266">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="4815b-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="4815b-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="4815b-267">CompositeFileProvider</span></span>

<span data-ttu-id="4815b-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="4815b-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="4815b-269">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="4815b-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="4815b-270">En la aplicación de ejemplo, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="4815b-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="4815b-271">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="4815b-271">Watch for changes</span></span>

<span data-ttu-id="4815b-272">El método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="4815b-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="4815b-273">`Watch` acepta una cadena de ruta de acceso, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="4815b-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="4815b-274">`Watch` devuelve un valor de <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="4815b-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="4815b-275">El token de cambio expone:</span><span class="sxs-lookup"><span data-stu-id="4815b-275">The change token exposes:</span></span>

* <span data-ttu-id="4815b-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="4815b-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="4815b-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="4815b-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="4815b-278">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="4815b-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="4815b-279">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="4815b-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="4815b-280">En la aplicación de ejemplo, la aplicación de consola *WatchConsole* se configura para mostrar un mensaje cada vez que se modifica un archivo de texto:</span><span class="sxs-lookup"><span data-stu-id="4815b-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="4815b-281">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="4815b-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="4815b-282">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="4815b-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="4815b-283">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="4815b-283">Glob patterns</span></span>

<span data-ttu-id="4815b-284">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="4815b-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="4815b-285">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="4815b-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="4815b-286">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="4815b-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="4815b-287">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="4815b-288">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="4815b-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="4815b-289">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="4815b-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="4815b-290">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="4815b-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="4815b-291">**Ejemplos de patrones globales**</span><span class="sxs-lookup"><span data-stu-id="4815b-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="4815b-292">Coincide con un archivo concreto en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="4815b-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="4815b-293">Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="4815b-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="4815b-294">Coincide con todos los archivos `appsettings.json` que estén en directorios exactamente un nivel por debajo de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="4815b-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="4815b-295">Coincide con todos los archivos que tengan la extensión *.txt* y se encuentren en cualquier lugar de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="4815b-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
