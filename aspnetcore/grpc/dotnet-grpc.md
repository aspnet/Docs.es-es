---
title: Administración de referencias de Protobuf con dotnet-grpc
author: juntaoluo
description: Obtenga información sobre cómo agregar, actualizar, eliminar y enumerar referencias de Protobuf con la herramienta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059915"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="dad87-103">Administración de referencias de Protobuf con dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="dad87-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="dad87-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="dad87-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="dad87-105">`dotnet-grpc` es una herramienta global de .NET Core para administrar referencias de [Protobuf ( *.proto* )](xref:grpc/basics#proto-file) dentro de un proyecto gRPC de .NET.</span><span class="sxs-lookup"><span data-stu-id="dad87-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf ( *.proto* )](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="dad87-106">La herramienta se puede usar para agregar, actualizar, quitar y enumerar las referencias de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="dad87-107">Instalación</span><span class="sxs-lookup"><span data-stu-id="dad87-107">Installation</span></span>

<span data-ttu-id="dad87-108">Para instalar la [herramienta global de .NET Core](/dotnet/core/tools/global-tools) `dotnet-grpc`, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="dad87-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="dad87-109">Agregar referencias</span><span class="sxs-lookup"><span data-stu-id="dad87-109">Add references</span></span>

<span data-ttu-id="dad87-110">`dotnet-grpc` se puede usar para agregar referencias de Protobuf como elementos `<Protobuf />` al archivo *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="dad87-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="dad87-111">Las referencias de Protobuf se usan para generar los recursos de cliente o servidor de C#.</span><span class="sxs-lookup"><span data-stu-id="dad87-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="dad87-112">La herramienta `dotnet-grpc` puede:</span><span class="sxs-lookup"><span data-stu-id="dad87-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="dad87-113">Crear una referencia de Protobuf a partir de archivos locales en el disco.</span><span class="sxs-lookup"><span data-stu-id="dad87-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="dad87-114">Crear una referencia de Protobuf a partir de un archivo remoto especificado por una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="dad87-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="dad87-115">Asegurarse de que se han agregado al proyecto las dependencias de paquete gRPC correctas.</span><span class="sxs-lookup"><span data-stu-id="dad87-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="dad87-116">Por ejemplo, el paquete `Grpc.AspNetCore` se agrega a una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="dad87-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="dad87-117">`Grpc.AspNetCore` contiene bibliotecas de cliente y servidor de gRPC, y compatibilidad con herramientas.</span><span class="sxs-lookup"><span data-stu-id="dad87-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="dad87-118">Como alternativa, los paquetes `Grpc.Net.Client`, `Grpc.Tools` y `Google.Protobuf`, que contienen solo las bibliotecas de cliente de gRPC y la compatibilidad con herramientas, se agregan a una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="dad87-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="dad87-119">Agregar archivo</span><span class="sxs-lookup"><span data-stu-id="dad87-119">Add file</span></span>

<span data-ttu-id="dad87-120">El comando `add-file` se usa para agregar archivos locales en el disco como referencias de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="dad87-121">Las rutas de acceso de archivo proporcionadas:</span><span class="sxs-lookup"><span data-stu-id="dad87-121">The file paths provided:</span></span>

* <span data-ttu-id="dad87-122">Pueden ser relativas al directorio actual o rutas de acceso absolutas.</span><span class="sxs-lookup"><span data-stu-id="dad87-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="dad87-123">Puede contener caracteres comodín para [patrones globales](https://wikipedia.org/wiki/Glob_(programming)) de archivo basados en patrones.</span><span class="sxs-lookup"><span data-stu-id="dad87-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="dad87-124">Si algún archivo está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta `Protos` de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dad87-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="dad87-125">Uso</span><span class="sxs-lookup"><span data-stu-id="dad87-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="dad87-126">Argumentos</span><span class="sxs-lookup"><span data-stu-id="dad87-126">Arguments</span></span>

| <span data-ttu-id="dad87-127">Argumento</span><span class="sxs-lookup"><span data-stu-id="dad87-127">Argument</span></span> | <span data-ttu-id="dad87-128">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-128">Description</span></span> |
|-|-|
| <span data-ttu-id="dad87-129">archivos</span><span class="sxs-lookup"><span data-stu-id="dad87-129">files</span></span> | <span data-ttu-id="dad87-130">Las referencias de archivo de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-130">The protobuf file references.</span></span> <span data-ttu-id="dad87-131">Pueden ser una ruta de acceso a patrones para los archivos Protobuf locales.</span><span class="sxs-lookup"><span data-stu-id="dad87-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="dad87-132">Opciones</span><span class="sxs-lookup"><span data-stu-id="dad87-132">Options</span></span>

| <span data-ttu-id="dad87-133">Opción corta</span><span class="sxs-lookup"><span data-stu-id="dad87-133">Short option</span></span> | <span data-ttu-id="dad87-134">Opción larga</span><span class="sxs-lookup"><span data-stu-id="dad87-134">Long option</span></span> | <span data-ttu-id="dad87-135">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="dad87-136">-p</span><span class="sxs-lookup"><span data-stu-id="dad87-136">-p</span></span> | <span data-ttu-id="dad87-137">--project</span><span class="sxs-lookup"><span data-stu-id="dad87-137">--project</span></span> | <span data-ttu-id="dad87-138">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="dad87-138">The path to the project file to operate on.</span></span> <span data-ttu-id="dad87-139">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="dad87-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="dad87-140">-S</span><span class="sxs-lookup"><span data-stu-id="dad87-140">-s</span></span> | <span data-ttu-id="dad87-141">--services</span><span class="sxs-lookup"><span data-stu-id="dad87-141">--services</span></span> | <span data-ttu-id="dad87-142">El tipo de servicios gRPC que se deben generar.</span><span class="sxs-lookup"><span data-stu-id="dad87-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="dad87-143">Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web.</span><span class="sxs-lookup"><span data-stu-id="dad87-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="dad87-144">Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="dad87-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="dad87-145">-i</span><span class="sxs-lookup"><span data-stu-id="dad87-145">-i</span></span> | <span data-ttu-id="dad87-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="dad87-146">--additional-import-dirs</span></span> | <span data-ttu-id="dad87-147">Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="dad87-148">Es una lista de rutas de acceso separadas por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="dad87-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="dad87-149">--access</span><span class="sxs-lookup"><span data-stu-id="dad87-149">--access</span></span> | <span data-ttu-id="dad87-150">El modificador de acceso que se va a usar para las clases generadas de C#.</span><span class="sxs-lookup"><span data-stu-id="dad87-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="dad87-151">El valor predeterminado es `Public`.</span><span class="sxs-lookup"><span data-stu-id="dad87-151">The default value is `Public`.</span></span> <span data-ttu-id="dad87-152">Los valores aceptados son: `Internal` y `Public`.</span><span class="sxs-lookup"><span data-stu-id="dad87-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="dad87-153">Agregar dirección URL</span><span class="sxs-lookup"><span data-stu-id="dad87-153">Add URL</span></span>

<span data-ttu-id="dad87-154">El comando `add-url` se usa para agregar un archivo remoto especificado por una dirección URL de origen como referencia de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="dad87-155">Se debe proporcionar una ruta de acceso de archivo para especificar dónde descargar el archivo remoto.</span><span class="sxs-lookup"><span data-stu-id="dad87-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="dad87-156">La ruta de acceso puede ser relativa al directorio actual o absoluta.</span><span class="sxs-lookup"><span data-stu-id="dad87-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="dad87-157">Si la ruta de acceso está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta virtual `Protos` de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dad87-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="dad87-158">Uso</span><span class="sxs-lookup"><span data-stu-id="dad87-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="dad87-159">Argumentos</span><span class="sxs-lookup"><span data-stu-id="dad87-159">Arguments</span></span>

| <span data-ttu-id="dad87-160">Argumento</span><span class="sxs-lookup"><span data-stu-id="dad87-160">Argument</span></span> | <span data-ttu-id="dad87-161">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-161">Description</span></span> |
|-|-|
| <span data-ttu-id="dad87-162">url</span><span class="sxs-lookup"><span data-stu-id="dad87-162">url</span></span> | <span data-ttu-id="dad87-163">La dirección URL de un archivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="dad87-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="dad87-164">Opciones</span><span class="sxs-lookup"><span data-stu-id="dad87-164">Options</span></span>

| <span data-ttu-id="dad87-165">Opción corta</span><span class="sxs-lookup"><span data-stu-id="dad87-165">Short option</span></span> | <span data-ttu-id="dad87-166">Opción larga</span><span class="sxs-lookup"><span data-stu-id="dad87-166">Long option</span></span> | <span data-ttu-id="dad87-167">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="dad87-168">-o</span><span class="sxs-lookup"><span data-stu-id="dad87-168">-o</span></span> | <span data-ttu-id="dad87-169">--output</span><span class="sxs-lookup"><span data-stu-id="dad87-169">--output</span></span> | <span data-ttu-id="dad87-170">Especifica la ruta de descarga para el archivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="dad87-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="dad87-171">Esta es una opción necesaria.</span><span class="sxs-lookup"><span data-stu-id="dad87-171">This is a required option.</span></span>
| <span data-ttu-id="dad87-172">-p</span><span class="sxs-lookup"><span data-stu-id="dad87-172">-p</span></span> | <span data-ttu-id="dad87-173">--project</span><span class="sxs-lookup"><span data-stu-id="dad87-173">--project</span></span> | <span data-ttu-id="dad87-174">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="dad87-174">The path to the project file to operate on.</span></span> <span data-ttu-id="dad87-175">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="dad87-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="dad87-176">-S</span><span class="sxs-lookup"><span data-stu-id="dad87-176">-s</span></span> | <span data-ttu-id="dad87-177">--services</span><span class="sxs-lookup"><span data-stu-id="dad87-177">--services</span></span> | <span data-ttu-id="dad87-178">El tipo de servicios gRPC que se deben generar.</span><span class="sxs-lookup"><span data-stu-id="dad87-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="dad87-179">Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web.</span><span class="sxs-lookup"><span data-stu-id="dad87-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="dad87-180">Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="dad87-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="dad87-181">-i</span><span class="sxs-lookup"><span data-stu-id="dad87-181">-i</span></span> | <span data-ttu-id="dad87-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="dad87-182">--additional-import-dirs</span></span> | <span data-ttu-id="dad87-183">Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="dad87-184">Es una lista de rutas de acceso separadas por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="dad87-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="dad87-185">--access</span><span class="sxs-lookup"><span data-stu-id="dad87-185">--access</span></span> | <span data-ttu-id="dad87-186">El modificador de acceso que se va a usar para las clases generadas de C#.</span><span class="sxs-lookup"><span data-stu-id="dad87-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="dad87-187">El valor predeterminado es `Public`.</span><span class="sxs-lookup"><span data-stu-id="dad87-187">Default value is `Public`.</span></span> <span data-ttu-id="dad87-188">Los valores aceptados son: `Internal` y `Public`.</span><span class="sxs-lookup"><span data-stu-id="dad87-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="dad87-189">Quitar</span><span class="sxs-lookup"><span data-stu-id="dad87-189">Remove</span></span>

<span data-ttu-id="dad87-190">El comando `remove` se usa para quitar las referencias de Protobuf del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="dad87-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="dad87-191">El comando acepta argumentos de ruta de acceso y direcciones URL de origen como argumentos.</span><span class="sxs-lookup"><span data-stu-id="dad87-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="dad87-192">La herramienta:</span><span class="sxs-lookup"><span data-stu-id="dad87-192">The tool:</span></span>

* <span data-ttu-id="dad87-193">Solo quita la referencia de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="dad87-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="dad87-194">No elimina el archivo *.proto* , incluso si se ha descargado originalmente desde una dirección URL remota.</span><span class="sxs-lookup"><span data-stu-id="dad87-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="dad87-195">Uso</span><span class="sxs-lookup"><span data-stu-id="dad87-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="dad87-196">Argumentos</span><span class="sxs-lookup"><span data-stu-id="dad87-196">Arguments</span></span>

| <span data-ttu-id="dad87-197">Argumento</span><span class="sxs-lookup"><span data-stu-id="dad87-197">Argument</span></span> | <span data-ttu-id="dad87-198">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-198">Description</span></span> |
|-|-|
| <span data-ttu-id="dad87-199">referencias</span><span class="sxs-lookup"><span data-stu-id="dad87-199">references</span></span> | <span data-ttu-id="dad87-200">Las direcciones URL o rutas de acceso de archivo de las referencias de Protobuf que se van a quitar.</span><span class="sxs-lookup"><span data-stu-id="dad87-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="dad87-201">Opciones</span><span class="sxs-lookup"><span data-stu-id="dad87-201">Options</span></span>

| <span data-ttu-id="dad87-202">Opción corta</span><span class="sxs-lookup"><span data-stu-id="dad87-202">Short option</span></span> | <span data-ttu-id="dad87-203">Opción larga</span><span class="sxs-lookup"><span data-stu-id="dad87-203">Long option</span></span> | <span data-ttu-id="dad87-204">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="dad87-205">-p</span><span class="sxs-lookup"><span data-stu-id="dad87-205">-p</span></span> | <span data-ttu-id="dad87-206">--project</span><span class="sxs-lookup"><span data-stu-id="dad87-206">--project</span></span> | <span data-ttu-id="dad87-207">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="dad87-207">The path to the project file to operate on.</span></span> <span data-ttu-id="dad87-208">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="dad87-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="dad87-209">Refresh</span><span class="sxs-lookup"><span data-stu-id="dad87-209">Refresh</span></span>

<span data-ttu-id="dad87-210">El comando `refresh` se usa para actualizar una referencia remota con el contenido más reciente de la dirección URL de origen.</span><span class="sxs-lookup"><span data-stu-id="dad87-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="dad87-211">Tanto la ruta de acceso del archivo de descarga como la dirección URL de origen se pueden usar para especificar la referencia que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="dad87-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="dad87-212">Nota:</span><span class="sxs-lookup"><span data-stu-id="dad87-212">Note:</span></span>

* <span data-ttu-id="dad87-213">Los códigos hash del contenido del archivo se comparan para determinar si se debe actualizar el archivo local.</span><span class="sxs-lookup"><span data-stu-id="dad87-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="dad87-214">No se compara la información de marca de tiempo.</span><span class="sxs-lookup"><span data-stu-id="dad87-214">No timestamp information is compared.</span></span>

<span data-ttu-id="dad87-215">La herramienta siempre reemplaza el archivo local con el archivo remoto si se necesita una actualización.</span><span class="sxs-lookup"><span data-stu-id="dad87-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="dad87-216">Uso</span><span class="sxs-lookup"><span data-stu-id="dad87-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="dad87-217">Argumentos</span><span class="sxs-lookup"><span data-stu-id="dad87-217">Arguments</span></span>

| <span data-ttu-id="dad87-218">Argumento</span><span class="sxs-lookup"><span data-stu-id="dad87-218">Argument</span></span> | <span data-ttu-id="dad87-219">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-219">Description</span></span> |
|-|-|
| <span data-ttu-id="dad87-220">referencias</span><span class="sxs-lookup"><span data-stu-id="dad87-220">references</span></span> | <span data-ttu-id="dad87-221">Las direcciones URL o las rutas de acceso de archivo a referencias de Protobuf remotas que se deben actualizar.</span><span class="sxs-lookup"><span data-stu-id="dad87-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="dad87-222">Deje este argumento vacío para actualizar todas las referencias remotas.</span><span class="sxs-lookup"><span data-stu-id="dad87-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="dad87-223">Opciones</span><span class="sxs-lookup"><span data-stu-id="dad87-223">Options</span></span>

| <span data-ttu-id="dad87-224">Opción corta</span><span class="sxs-lookup"><span data-stu-id="dad87-224">Short option</span></span> | <span data-ttu-id="dad87-225">Opción larga</span><span class="sxs-lookup"><span data-stu-id="dad87-225">Long option</span></span> | <span data-ttu-id="dad87-226">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="dad87-227">-p</span><span class="sxs-lookup"><span data-stu-id="dad87-227">-p</span></span> | <span data-ttu-id="dad87-228">--project</span><span class="sxs-lookup"><span data-stu-id="dad87-228">--project</span></span> | <span data-ttu-id="dad87-229">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="dad87-229">The path to the project file to operate on.</span></span> <span data-ttu-id="dad87-230">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="dad87-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="dad87-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="dad87-231">--dry-run</span></span> | <span data-ttu-id="dad87-232">Genera una lista de archivos que se actualizarían sin descargar ningún contenido nuevo.</span><span class="sxs-lookup"><span data-stu-id="dad87-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="dad87-233">Lista</span><span class="sxs-lookup"><span data-stu-id="dad87-233">List</span></span>

<span data-ttu-id="dad87-234">El comando `list` se usa para mostrar todas las referencias de Protobuf en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="dad87-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="dad87-235">Si todos los valores de una columna son valores predeterminados, la columna se puede omitir.</span><span class="sxs-lookup"><span data-stu-id="dad87-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="dad87-236">Uso</span><span class="sxs-lookup"><span data-stu-id="dad87-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="dad87-237">Opciones</span><span class="sxs-lookup"><span data-stu-id="dad87-237">Options</span></span>

| <span data-ttu-id="dad87-238">Opción corta</span><span class="sxs-lookup"><span data-stu-id="dad87-238">Short option</span></span> | <span data-ttu-id="dad87-239">Opción larga</span><span class="sxs-lookup"><span data-stu-id="dad87-239">Long option</span></span> | <span data-ttu-id="dad87-240">Descripción</span><span class="sxs-lookup"><span data-stu-id="dad87-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="dad87-241">-p</span><span class="sxs-lookup"><span data-stu-id="dad87-241">-p</span></span> | <span data-ttu-id="dad87-242">--project</span><span class="sxs-lookup"><span data-stu-id="dad87-242">--project</span></span> | <span data-ttu-id="dad87-243">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="dad87-243">The path to the project file to operate on.</span></span> <span data-ttu-id="dad87-244">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="dad87-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dad87-245">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="dad87-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
