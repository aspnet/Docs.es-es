---
title: Desarrollo de aplicaciones ASP.NET Core con OpenAPI
author: ryanbrandenburg
description: Muestra cómo usar la herramienta "Microsoft.dotnet-openapi" para agregar referencias a archivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 6a9b80e868a54bd76503a6421c34ae159421699b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022243"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Desarrollo de aplicaciones ASP.NET Core con herramientas de OpenAPI

Por Ryan Brandenburg

[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) es una [herramienta global de .NET Core](/dotnet/core/tools/global-tools) para administrar las referencias de [OpenAPI](https://github.com/OAI/OpenAPI-Specification) dentro de un proyecto.

## <a name="installation"></a>Instalación

Para instalar `Microsoft.dotnet-openapi`, ejecute el siguiente comando:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Sumar

Al agregar una referencia OpenAPI mediante cualquiera de los comandos de esta página, se agrega un `<OpenApiReference />` elemento similar al siguiente al archivo *. csproj* :

```xml
<OpenApiReference Include="openapi.json" />
```

La referencia anterior es necesaria para que la aplicación llame al código de cliente generado.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Agregar archivo

#### <a name="options"></a>Opciones

| Opción corta| Opción larga| Descripción | Ejemplo |
|-------|------|-------|---------|
| -p|--updateProject | El proyecto sobre el que actuar. |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--code-generator| El generador de código que se va a aplicar a la referencia. Las opciones son `NSwagCSharp` y `NSwagTypeScript`. Si no se especifica `--code-generator`, la herramienta usa `NSwagCSharp` de forma predeterminada.|dotnet openapi add file .\OpenApi.json --code-generator
| -H|--help|Mostrar información de ayuda.|dotnet openapi add file --help|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descripción | Ejemplo |
|-------------|-------------|---------|
| source-file | El origen a partir del cual se va a crear una referencia. Debe ser un archivo de OpenAPI. |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>Agregar dirección URL

#### <a name="options"></a>Opciones

| Opción corta| Opción larga| Descripción | Ejemplo |
|-------|------|-------------|---------|
| -p|--updateProject | El proyecto sobre el que actuar. |dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--output-file | Dónde colocar la copia local del archivo OpenAPI. |dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--code-generator| El generador de código que se va a aplicar a la referencia. Las opciones son `NSwagCSharp` y `NSwagTypeScript`. |dotnet openapi add file .\OpenApi.json --code-generator
| -H|--help|Mostrar información de ayuda.|dotnet openapi add url --help|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descripción | Ejemplo |
|-------------|-------------|---------|
| source-URL | El origen a partir del cual se va a crear una referencia. Debe ser una dirección URL. |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>Quitar

Quita la referencia de OpenAPI que coincide con el nombre de archivo dado del archivo *.csproj*. Cuando la referencia de OpenAPI se quita, no se generarán los clientes. Los archivos *.json* y *.yaml* locales se eliminan.

### <a name="options"></a>Opciones

| Opción corta| Opción larga| Descripción| Ejemplo |
|-------|------|------------|---------|
| -p|--updateProject | El proyecto sobre el que actuar. |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -H|--help|Mostrar información de ayuda.|dotnet openapi remove --help|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descripción| Ejemplo |
| ------------|------------|---------|
| source-file | Origen al que se va a quitar la referencia. |dotnet openapi remove *.\OpenAPI.json* |

## <a name="refresh"></a>Actualizar

Actualiza la versión local de un archivo que se descargó usando el contenido más reciente de la dirección URL de descarga.

### <a name="options"></a>Opciones

| Opción corta| Opción larga| Descripción | Ejemplo |
|-------|------|-------------|---------|
| -p|--updateProject | El proyecto sobre el que actuar. | dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -H|--help|Mostrar información de ayuda.|dotnet openapi refresh --help|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descripción | Ejemplo |
| ------------|-------------|---------|
| source-URL | Dirección URL desde la que se va a actualizar la referencia. | dotnet openapi refresh `https://contoso.com/openapi.json` |
