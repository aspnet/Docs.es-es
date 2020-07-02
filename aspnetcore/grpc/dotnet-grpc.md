---
title: Administración de referencias de Protobuf con dotnet-grpc
author: juntaoluo
description: Obtenga información sobre cómo agregar, actualizar, eliminar y enumerar referencias de Protobuf con la herramienta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: 1e4e5cd6a4a687ecacf0c4803784e68aee228498
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404215"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="72733-103">Administración de referencias de Protobuf con dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="72733-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="72733-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="72733-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="72733-105">`dotnet-grpc` es una herramienta global de .NET Core para administrar referencias de [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) dentro de un proyecto gRPC de .NET.</span><span class="sxs-lookup"><span data-stu-id="72733-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="72733-106">La herramienta se puede usar para agregar, actualizar, quitar y enumerar las referencias de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="72733-107">Instalación</span><span class="sxs-lookup"><span data-stu-id="72733-107">Installation</span></span>

<span data-ttu-id="72733-108">Para instalar la [herramienta global de .NET Core](/dotnet/core/tools/global-tools) `dotnet-grpc`, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="72733-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="72733-109">Agregar referencias</span><span class="sxs-lookup"><span data-stu-id="72733-109">Add references</span></span>

<span data-ttu-id="72733-110">`dotnet-grpc` se puede usar para agregar referencias de Protobuf como elementos `<Protobuf />` al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="72733-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="72733-111">Las referencias de Protobuf se usan para generar los recursos de cliente o servidor de C#.</span><span class="sxs-lookup"><span data-stu-id="72733-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="72733-112">La herramienta `dotnet-grpc` puede:</span><span class="sxs-lookup"><span data-stu-id="72733-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="72733-113">Crear una referencia de Protobuf a partir de archivos locales en el disco.</span><span class="sxs-lookup"><span data-stu-id="72733-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="72733-114">Crear una referencia de Protobuf a partir de un archivo remoto especificado por una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="72733-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="72733-115">Asegurarse de que se han agregado al proyecto las dependencias de paquete gRPC correctas.</span><span class="sxs-lookup"><span data-stu-id="72733-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="72733-116">Por ejemplo, el paquete `Grpc.AspNetCore` se agrega a una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="72733-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="72733-117">`Grpc.AspNetCore` contiene bibliotecas de cliente y servidor de gRPC, y compatibilidad con herramientas.</span><span class="sxs-lookup"><span data-stu-id="72733-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="72733-118">Como alternativa, los paquetes `Grpc.Net.Client`, `Grpc.Tools` y `Google.Protobuf`, que contienen solo las bibliotecas de cliente de gRPC y la compatibilidad con herramientas, se agregan a una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="72733-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="72733-119">Agregar archivo</span><span class="sxs-lookup"><span data-stu-id="72733-119">Add file</span></span>

<span data-ttu-id="72733-120">El comando `add-file` se usa para agregar archivos locales en el disco como referencias de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="72733-121">Las rutas de acceso de archivo proporcionadas:</span><span class="sxs-lookup"><span data-stu-id="72733-121">The file paths provided:</span></span>

* <span data-ttu-id="72733-122">Pueden ser relativas al directorio actual o rutas de acceso absolutas.</span><span class="sxs-lookup"><span data-stu-id="72733-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="72733-123">Puede contener caracteres comodín para [patrones globales](https://wikipedia.org/wiki/Glob_(programming)) de archivo basados en patrones.</span><span class="sxs-lookup"><span data-stu-id="72733-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="72733-124">Si algún archivo está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta `Protos` de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="72733-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="72733-125">Uso</span><span class="sxs-lookup"><span data-stu-id="72733-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="72733-126">Argumentos</span><span class="sxs-lookup"><span data-stu-id="72733-126">Arguments</span></span>

| <span data-ttu-id="72733-127">Argumento</span><span class="sxs-lookup"><span data-stu-id="72733-127">Argument</span></span> | <span data-ttu-id="72733-128">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-128">Description</span></span> |
|-|-|
| <span data-ttu-id="72733-129">archivos</span><span class="sxs-lookup"><span data-stu-id="72733-129">files</span></span> | <span data-ttu-id="72733-130">Las referencias de archivo de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-130">The protobuf file references.</span></span> <span data-ttu-id="72733-131">Pueden ser una ruta de acceso a patrones para los archivos Protobuf locales.</span><span class="sxs-lookup"><span data-stu-id="72733-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="72733-132">Opciones</span><span class="sxs-lookup"><span data-stu-id="72733-132">Options</span></span>

| <span data-ttu-id="72733-133">Opción corta</span><span class="sxs-lookup"><span data-stu-id="72733-133">Short option</span></span> | <span data-ttu-id="72733-134">Opción larga</span><span class="sxs-lookup"><span data-stu-id="72733-134">Long option</span></span> | <span data-ttu-id="72733-135">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="72733-136">-p</span><span class="sxs-lookup"><span data-stu-id="72733-136">-p</span></span> | <span data-ttu-id="72733-137">--project</span><span class="sxs-lookup"><span data-stu-id="72733-137">--project</span></span> | <span data-ttu-id="72733-138">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="72733-138">The path to the project file to operate on.</span></span> <span data-ttu-id="72733-139">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="72733-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="72733-140">-S</span><span class="sxs-lookup"><span data-stu-id="72733-140">-s</span></span> | <span data-ttu-id="72733-141">--services</span><span class="sxs-lookup"><span data-stu-id="72733-141">--services</span></span> | <span data-ttu-id="72733-142">El tipo de servicios gRPC que se deben generar.</span><span class="sxs-lookup"><span data-stu-id="72733-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="72733-143">Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web.</span><span class="sxs-lookup"><span data-stu-id="72733-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="72733-144">Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="72733-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="72733-145">-i</span><span class="sxs-lookup"><span data-stu-id="72733-145">-i</span></span> | <span data-ttu-id="72733-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="72733-146">--additional-import-dirs</span></span> | <span data-ttu-id="72733-147">Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="72733-148">Es una lista de rutas de acceso separadas por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="72733-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="72733-149">--access</span><span class="sxs-lookup"><span data-stu-id="72733-149">--access</span></span> | <span data-ttu-id="72733-150">El modificador de acceso que se va a usar para las clases generadas de C#.</span><span class="sxs-lookup"><span data-stu-id="72733-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="72733-151">El valor predeterminado es `Public`.</span><span class="sxs-lookup"><span data-stu-id="72733-151">The default value is `Public`.</span></span> <span data-ttu-id="72733-152">Los valores aceptados son: `Internal` y `Public`.</span><span class="sxs-lookup"><span data-stu-id="72733-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="72733-153">Agregar dirección URL</span><span class="sxs-lookup"><span data-stu-id="72733-153">Add URL</span></span>

<span data-ttu-id="72733-154">El comando `add-url` se usa para agregar un archivo remoto especificado por una dirección URL de origen como referencia de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="72733-155">Se debe proporcionar una ruta de acceso de archivo para especificar dónde descargar el archivo remoto.</span><span class="sxs-lookup"><span data-stu-id="72733-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="72733-156">La ruta de acceso puede ser relativa al directorio actual o absoluta.</span><span class="sxs-lookup"><span data-stu-id="72733-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="72733-157">Si la ruta de acceso está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta virtual `Protos` de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="72733-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="72733-158">Uso</span><span class="sxs-lookup"><span data-stu-id="72733-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="72733-159">Argumentos</span><span class="sxs-lookup"><span data-stu-id="72733-159">Arguments</span></span>

| <span data-ttu-id="72733-160">Argumento</span><span class="sxs-lookup"><span data-stu-id="72733-160">Argument</span></span> | <span data-ttu-id="72733-161">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-161">Description</span></span> |
|-|-|
| <span data-ttu-id="72733-162">url</span><span class="sxs-lookup"><span data-stu-id="72733-162">url</span></span> | <span data-ttu-id="72733-163">La dirección URL de un archivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="72733-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="72733-164">Opciones</span><span class="sxs-lookup"><span data-stu-id="72733-164">Options</span></span>

| <span data-ttu-id="72733-165">Opción corta</span><span class="sxs-lookup"><span data-stu-id="72733-165">Short option</span></span> | <span data-ttu-id="72733-166">Opción larga</span><span class="sxs-lookup"><span data-stu-id="72733-166">Long option</span></span> | <span data-ttu-id="72733-167">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="72733-168">-o</span><span class="sxs-lookup"><span data-stu-id="72733-168">-o</span></span> | <span data-ttu-id="72733-169">--output</span><span class="sxs-lookup"><span data-stu-id="72733-169">--output</span></span> | <span data-ttu-id="72733-170">Especifica la ruta de descarga para el archivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="72733-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="72733-171">Esta es una opción necesaria.</span><span class="sxs-lookup"><span data-stu-id="72733-171">This is a required option.</span></span>
| <span data-ttu-id="72733-172">-p</span><span class="sxs-lookup"><span data-stu-id="72733-172">-p</span></span> | <span data-ttu-id="72733-173">--project</span><span class="sxs-lookup"><span data-stu-id="72733-173">--project</span></span> | <span data-ttu-id="72733-174">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="72733-174">The path to the project file to operate on.</span></span> <span data-ttu-id="72733-175">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="72733-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="72733-176">-S</span><span class="sxs-lookup"><span data-stu-id="72733-176">-s</span></span> | <span data-ttu-id="72733-177">--services</span><span class="sxs-lookup"><span data-stu-id="72733-177">--services</span></span> | <span data-ttu-id="72733-178">El tipo de servicios gRPC que se deben generar.</span><span class="sxs-lookup"><span data-stu-id="72733-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="72733-179">Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web.</span><span class="sxs-lookup"><span data-stu-id="72733-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="72733-180">Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="72733-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="72733-181">-i</span><span class="sxs-lookup"><span data-stu-id="72733-181">-i</span></span> | <span data-ttu-id="72733-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="72733-182">--additional-import-dirs</span></span> | <span data-ttu-id="72733-183">Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="72733-184">Es una lista de rutas de acceso separadas por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="72733-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="72733-185">--access</span><span class="sxs-lookup"><span data-stu-id="72733-185">--access</span></span> | <span data-ttu-id="72733-186">El modificador de acceso que se va a usar para las clases generadas de C#.</span><span class="sxs-lookup"><span data-stu-id="72733-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="72733-187">El valor predeterminado es `Public`.</span><span class="sxs-lookup"><span data-stu-id="72733-187">Default value is `Public`.</span></span> <span data-ttu-id="72733-188">Los valores aceptados son: `Internal` y `Public`.</span><span class="sxs-lookup"><span data-stu-id="72733-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="72733-189">Quitar</span><span class="sxs-lookup"><span data-stu-id="72733-189">Remove</span></span>

<span data-ttu-id="72733-190">El comando `remove` se usa para quitar las referencias de Protobuf del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="72733-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="72733-191">El comando acepta argumentos de ruta de acceso y direcciones URL de origen como argumentos.</span><span class="sxs-lookup"><span data-stu-id="72733-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="72733-192">La herramienta:</span><span class="sxs-lookup"><span data-stu-id="72733-192">The tool:</span></span>

* <span data-ttu-id="72733-193">Solo quita la referencia de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="72733-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="72733-194">No elimina el archivo *.proto*, incluso si se ha descargado originalmente desde una dirección URL remota.</span><span class="sxs-lookup"><span data-stu-id="72733-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="72733-195">Uso</span><span class="sxs-lookup"><span data-stu-id="72733-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="72733-196">Argumentos</span><span class="sxs-lookup"><span data-stu-id="72733-196">Arguments</span></span>

| <span data-ttu-id="72733-197">Argumento</span><span class="sxs-lookup"><span data-stu-id="72733-197">Argument</span></span> | <span data-ttu-id="72733-198">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-198">Description</span></span> |
|-|-|
| <span data-ttu-id="72733-199">referencias</span><span class="sxs-lookup"><span data-stu-id="72733-199">references</span></span> | <span data-ttu-id="72733-200">Las direcciones URL o rutas de acceso de archivo de las referencias de Protobuf que se van a quitar.</span><span class="sxs-lookup"><span data-stu-id="72733-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="72733-201">Opciones</span><span class="sxs-lookup"><span data-stu-id="72733-201">Options</span></span>

| <span data-ttu-id="72733-202">Opción corta</span><span class="sxs-lookup"><span data-stu-id="72733-202">Short option</span></span> | <span data-ttu-id="72733-203">Opción larga</span><span class="sxs-lookup"><span data-stu-id="72733-203">Long option</span></span> | <span data-ttu-id="72733-204">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="72733-205">-p</span><span class="sxs-lookup"><span data-stu-id="72733-205">-p</span></span> | <span data-ttu-id="72733-206">--project</span><span class="sxs-lookup"><span data-stu-id="72733-206">--project</span></span> | <span data-ttu-id="72733-207">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="72733-207">The path to the project file to operate on.</span></span> <span data-ttu-id="72733-208">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="72733-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="72733-209">Refresh</span><span class="sxs-lookup"><span data-stu-id="72733-209">Refresh</span></span>

<span data-ttu-id="72733-210">El comando `refresh` se usa para actualizar una referencia remota con el contenido más reciente de la dirección URL de origen.</span><span class="sxs-lookup"><span data-stu-id="72733-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="72733-211">Tanto la ruta de acceso del archivo de descarga como la dirección URL de origen se pueden usar para especificar la referencia que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="72733-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="72733-212">Nota:</span><span class="sxs-lookup"><span data-stu-id="72733-212">Note:</span></span>

* <span data-ttu-id="72733-213">Los códigos hash del contenido del archivo se comparan para determinar si se debe actualizar el archivo local.</span><span class="sxs-lookup"><span data-stu-id="72733-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="72733-214">No se compara la información de marca de tiempo.</span><span class="sxs-lookup"><span data-stu-id="72733-214">No timestamp information is compared.</span></span>

<span data-ttu-id="72733-215">La herramienta siempre reemplaza el archivo local con el archivo remoto si se necesita una actualización.</span><span class="sxs-lookup"><span data-stu-id="72733-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="72733-216">Uso</span><span class="sxs-lookup"><span data-stu-id="72733-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="72733-217">Argumentos</span><span class="sxs-lookup"><span data-stu-id="72733-217">Arguments</span></span>

| <span data-ttu-id="72733-218">Argumento</span><span class="sxs-lookup"><span data-stu-id="72733-218">Argument</span></span> | <span data-ttu-id="72733-219">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-219">Description</span></span> |
|-|-|
| <span data-ttu-id="72733-220">referencias</span><span class="sxs-lookup"><span data-stu-id="72733-220">references</span></span> | <span data-ttu-id="72733-221">Las direcciones URL o las rutas de acceso de archivo a referencias de Protobuf remotas que se deben actualizar.</span><span class="sxs-lookup"><span data-stu-id="72733-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="72733-222">Deje este argumento vacío para actualizar todas las referencias remotas.</span><span class="sxs-lookup"><span data-stu-id="72733-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="72733-223">Opciones</span><span class="sxs-lookup"><span data-stu-id="72733-223">Options</span></span>

| <span data-ttu-id="72733-224">Opción corta</span><span class="sxs-lookup"><span data-stu-id="72733-224">Short option</span></span> | <span data-ttu-id="72733-225">Opción larga</span><span class="sxs-lookup"><span data-stu-id="72733-225">Long option</span></span> | <span data-ttu-id="72733-226">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="72733-227">-p</span><span class="sxs-lookup"><span data-stu-id="72733-227">-p</span></span> | <span data-ttu-id="72733-228">--project</span><span class="sxs-lookup"><span data-stu-id="72733-228">--project</span></span> | <span data-ttu-id="72733-229">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="72733-229">The path to the project file to operate on.</span></span> <span data-ttu-id="72733-230">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="72733-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="72733-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="72733-231">--dry-run</span></span> | <span data-ttu-id="72733-232">Genera una lista de archivos que se actualizarían sin descargar ningún contenido nuevo.</span><span class="sxs-lookup"><span data-stu-id="72733-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="72733-233">Lista</span><span class="sxs-lookup"><span data-stu-id="72733-233">List</span></span>

<span data-ttu-id="72733-234">El comando `list` se usa para mostrar todas las referencias de Protobuf en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="72733-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="72733-235">Si todos los valores de una columna son valores predeterminados, la columna se puede omitir.</span><span class="sxs-lookup"><span data-stu-id="72733-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="72733-236">Uso</span><span class="sxs-lookup"><span data-stu-id="72733-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="72733-237">Opciones</span><span class="sxs-lookup"><span data-stu-id="72733-237">Options</span></span>

| <span data-ttu-id="72733-238">Opción corta</span><span class="sxs-lookup"><span data-stu-id="72733-238">Short option</span></span> | <span data-ttu-id="72733-239">Opción larga</span><span class="sxs-lookup"><span data-stu-id="72733-239">Long option</span></span> | <span data-ttu-id="72733-240">Descripción</span><span class="sxs-lookup"><span data-stu-id="72733-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="72733-241">-p</span><span class="sxs-lookup"><span data-stu-id="72733-241">-p</span></span> | <span data-ttu-id="72733-242">--project</span><span class="sxs-lookup"><span data-stu-id="72733-242">--project</span></span> | <span data-ttu-id="72733-243">La ruta de acceso al archivo del proyecto sobre el que se va a actuar.</span><span class="sxs-lookup"><span data-stu-id="72733-243">The path to the project file to operate on.</span></span> <span data-ttu-id="72733-244">Si no se especifica un archivo, el comando busca uno en el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="72733-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72733-245">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="72733-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
