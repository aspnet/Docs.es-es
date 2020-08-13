---
title: Administración de referencias de Protobuf con dotnet-grpc
author: juntaoluo
description: Obtenga información sobre cómo agregar, actualizar, eliminar y enumerar referencias de Protobuf con la herramienta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: d41958d586f54d5944af187933f2b0248f763171
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016134"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Administración de referencias de Protobuf con dotnet-grpc

Por [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` es una herramienta global de .NET Core para administrar referencias de [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) dentro de un proyecto gRPC de .NET. La herramienta se puede usar para agregar, actualizar, quitar y enumerar las referencias de Protobuf.

## <a name="installation"></a>Instalación

Para instalar la [herramienta global de .NET Core](/dotnet/core/tools/global-tools) `dotnet-grpc`, ejecute el comando siguiente:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Agregar referencias

`dotnet-grpc` se puede usar para agregar referencias de Protobuf como elementos `<Protobuf />` al archivo *.csproj*:

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Las referencias de Protobuf se usan para generar los recursos de cliente o servidor de C#. La herramienta `dotnet-grpc` puede:

* Crear una referencia de Protobuf a partir de archivos locales en el disco.
* Crear una referencia de Protobuf a partir de un archivo remoto especificado por una dirección URL.
* Asegurarse de que se han agregado al proyecto las dependencias de paquete gRPC correctas.

Por ejemplo, el paquete `Grpc.AspNetCore` se agrega a una aplicación web. `Grpc.AspNetCore` contiene bibliotecas de cliente y servidor de gRPC, y compatibilidad con herramientas. Como alternativa, los paquetes `Grpc.Net.Client`, `Grpc.Tools` y `Google.Protobuf`, que contienen solo las bibliotecas de cliente de gRPC y la compatibilidad con herramientas, se agregan a una aplicación de consola.

### <a name="add-file"></a>Agregar archivo

El comando `add-file` se usa para agregar archivos locales en el disco como referencias de Protobuf. Las rutas de acceso de archivo proporcionadas:

* Pueden ser relativas al directorio actual o rutas de acceso absolutas.
* Puede contener caracteres comodín para [patrones globales](https://wikipedia.org/wiki/Glob_(programming)) de archivo basados en patrones.

Si algún archivo está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta `Protos` de Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumentos

| Argumento | Descripción |
|-|-|
| archivos | Las referencias de archivo de Protobuf. Pueden ser una ruta de acceso a patrones para los archivos Protobuf locales. |

#### <a name="options"></a>Opciones

| Opción corta | Opción larga | Descripción |
|-|-|-|
| -p | --project | La ruta de acceso al archivo del proyecto sobre el que se va a actuar. Si no se especifica un archivo, el comando busca uno en el directorio actual.
| -S | --services | El tipo de servicios gRPC que se deben generar. Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web. Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --additional-import-dirs | Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf. Es una lista de rutas de acceso separadas por punto y coma.
| | --access | El modificador de acceso que se va a usar para las clases generadas de C#. El valor predeterminado es `Public`. Los valores aceptados son: `Internal` y `Public`.

### <a name="add-url"></a>Agregar dirección URL

El comando `add-url` se usa para agregar un archivo remoto especificado por una dirección URL de origen como referencia de Protobuf. Se debe proporcionar una ruta de acceso de archivo para especificar dónde descargar el archivo remoto. La ruta de acceso puede ser relativa al directorio actual o absoluta. Si la ruta de acceso está fuera del directorio del proyecto, se agrega un elemento `Link` para mostrar el archivo en la carpeta virtual `Protos` de Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumentos

| Argumento | Descripción |
|-|-|
| url | La dirección URL de un archivo protobuf remoto. |

#### <a name="options"></a>Opciones

| Opción corta | Opción larga | Descripción |
|-|-|-|
| -o | --output | Especifica la ruta de descarga para el archivo protobuf remoto. Esta es una opción necesaria.
| -p | --project | La ruta de acceso al archivo del proyecto sobre el que se va a actuar. Si no se especifica un archivo, el comando busca uno en el directorio actual.
| -S | --services | El tipo de servicios gRPC que se deben generar. Si se especifica `Default`, se usa `Both` para los proyectos web y `Client` para los que no son web. Los valores aceptados son `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --additional-import-dirs | Directorios adicionales que se usarán al resolver importaciones para los archivos de Protobuf. Es una lista de rutas de acceso separadas por punto y coma.
| | --access | El modificador de acceso que se va a usar para las clases generadas de C#. El valor predeterminado es `Public`. Los valores aceptados son: `Internal` y `Public`.

## <a name="remove"></a>Quitar

El comando `remove` se usa para quitar las referencias de Protobuf del archivo *.csproj*. El comando acepta argumentos de ruta de acceso y direcciones URL de origen como argumentos. La herramienta:

* Solo quita la referencia de Protobuf.
* No elimina el archivo *.proto*, incluso si se ha descargado originalmente desde una dirección URL remota.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumentos

| Argumento | Descripción |
|-|-|
| referencias | Las direcciones URL o rutas de acceso de archivo de las referencias de Protobuf que se van a quitar. |

### <a name="options"></a>Opciones

| Opción corta | Opción larga | Descripción |
|-|-|-|
| -p | --project | La ruta de acceso al archivo del proyecto sobre el que se va a actuar. Si no se especifica un archivo, el comando busca uno en el directorio actual.

## <a name="refresh"></a>Refresh

El comando `refresh` se usa para actualizar una referencia remota con el contenido más reciente de la dirección URL de origen. Tanto la ruta de acceso del archivo de descarga como la dirección URL de origen se pueden usar para especificar la referencia que se va a actualizar. Nota:

* Los códigos hash del contenido del archivo se comparan para determinar si se debe actualizar el archivo local.
* No se compara la información de marca de tiempo.

La herramienta siempre reemplaza el archivo local con el archivo remoto si se necesita una actualización.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumentos

| Argumento | Descripción |
|-|-|
| referencias | Las direcciones URL o las rutas de acceso de archivo a referencias de Protobuf remotas que se deben actualizar. Deje este argumento vacío para actualizar todas las referencias remotas. |

### <a name="options"></a>Opciones

| Opción corta | Opción larga | Descripción |
|-|-|-|
| -p | --project | La ruta de acceso al archivo del proyecto sobre el que se va a actuar. Si no se especifica un archivo, el comando busca uno en el directorio actual.
| | --dry-run | Genera una lista de archivos que se actualizarían sin descargar ningún contenido nuevo.

## <a name="list"></a>Lista

El comando `list` se usa para mostrar todas las referencias de Protobuf en el archivo de proyecto. Si todos los valores de una columna son valores predeterminados, la columna se puede omitir.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opciones

| Opción corta | Opción larga | Descripción |
|-|-|-|
| -p | --project | La ruta de acceso al archivo del proyecto sobre el que se va a actuar. Si no se especifica un archivo, el comando busca uno en el directorio actual.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
