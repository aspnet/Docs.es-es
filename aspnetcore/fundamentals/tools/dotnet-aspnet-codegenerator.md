---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: El comando dotnet aspnet-codegenerator aplica scaffolding a los proyectos de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 071f2269081e63ad1355547bccb449180c59c997
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016510"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`: ejecuta el motor de scaffolding de ASP.NET Core. `dotnet aspnet-codegenerator` solo es necesario para aplicar scaffolding desde la línea de comandos, no es necesario para usar la técnica de scaffolding con Visual Studio.

Este artículo se aplica al [SDK de .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) y versiones posteriores.

## <a name="installing-aspnet-codegenerator"></a>Instalación de aspnet-codegenerator

`dotnet-aspnet-codegenerator` es una [herramienta global](/dotnet/core/tools/global-tools) que debe estar instalada. El comando siguiente instala la versión estable más reciente de la herramienta `dotnet-aspnet-codegenerator`:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

El comando siguiente actualiza `dotnet-aspnet-codegenerator` a la versión estable más reciente disponible desde los SDK instalados de .NET Core:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Sinopsis

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Descripción

El comando `dotnet aspnet-codegenerator` global ejecuta el generador de código de ASP.NET Core y el motor de scaffolding.

## <a name="arguments"></a>Argumentos

`generator`

El generador de código que se va a ejecutar. Estos generadores están disponibles:

| Generator  | Operación                                                            |
| ---------- | -------------------------------------------------------------------- |
| área       | [Aplica scaffolding a un área](xref:mvc/controllers/areas)                      |
| controlador | [Aplica scaffolding a un controlador](xref:tutorials/first-mvc-app/adding-model)  |
| identidad   | [Aplica scaffolding a Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Aplica scaffolding a Razor Pages](xref:tutorials/razor-pages/model)            |
| vista       | [Aplica scaffolding a una vista](xref:mvc/views/overview)                          |

## <a name="options"></a>Opciones

`-n|--nuget-package-dir`

Especifica el directorio del paquete de NuGet.

`-c|--configuration {Debug|Release}`

Define la configuración de compilación. El valor predeterminado es `Debug`.

`-tfm|--target-framework`

Establece como destino el [marco](/dotnet/standard/frameworks) que se va a usar. Por ejemplo: `net46`.

`-b|--build-base-path`

La ruta de acceso de la base de compilación.

`-h|--help`

Imprime una corta ayuda para el comando.

`--no-build`

No compila el proyecto antes de ejecutarlo. También establece la marca `--no-restore` de forma implícita.

`-p|--project <PATH>`

Especifica la ruta de acceso del archivo del proyecto que se va a ejecutar (nombre de la carpeta o ruta de acceso completa). Si no se especifica, se toma como predeterminado el directorio actual.

## <a name="generator-options"></a>Opciones del generador

En las secciones siguientes se detallan las opciones disponibles para los generadores compatibles:

* Área
* Controlador
* Identity  
* Razorpage
* Ver

<a name="area"></a>

### <a name="area-options"></a>Opciones del área

Esta herramienta está diseñada para los proyectos web de ASP.NET Core con controladores y vistas. No está pensada para las aplicaciones de Razor Pages.

Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`

El comando anterior genera estas carpetas:

* *Áreas*
  * *AreaNameToGenerate*
    * *Controladores*
    * *Data*
    * *Models*
    * *Vistas*

<a name="ctl"></a>

### <a name="controller-options"></a>Opciones del controlador

En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `controller` y `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator controller`:

| Opción                         | Descripción                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName o -name      | El nombre del controlador.                                                                                   |
| --useAsyncActions o -async    | Genera acciones asincrónicas del controlador.                                                                        |
| --noViews o -nv               | **No** genera vistas.                                                                                    |
| --restWithNoViews o -api      | Genera un controlador con una API de estilo REST. Se supone `noViews` y todas las opciones relacionadas con la vista se omiten. |
| --readWriteActions o -actions | Genera un controlador con acciones de lectura/escritura sin un modelo.                                              |

Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator controller`:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/first-mvc-app/adding-model) para ver un ejemplo de `dotnet aspnet-codegenerator controller`.

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

Es posible aplicar scaffolding a Razor Pages de manera individual si se especifica el nombre de la página nueva y la plantilla a usar. Las plantillas admitidas son:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Por ejemplo, el comando siguiente usa la plantilla de edición para generar *MyEdit.cshtml* y *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Por lo general, no se especifica la plantilla ni el nombre del archivo generado y se crean estas plantillas:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `razorpage` y `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator razorpage`:

| Opción                        | Descripción                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName o -namespace | El nombre del espacio de nombres que se usará para la clase PageModel generada.                          |
| --partialView o -partial     | Genera una vista parcial. Si se especifica, las opciones de diseño -l y -udl se ignoran. |
| --noPageModel o -npm         | Cambia a no generar una clase PageModel para una plantilla vacía.                           |

Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model) para ver un ejemplo de `dotnet aspnet-codegenerator razorpage`.

### Identity

Vea [Scaffolding Identity](xref:security/authentication/scaffold-identity).
