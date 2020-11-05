---
title: SDK de Razor de ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo Razor Pages en ASP.NET Core facilita la programación en escenarios centrados en páginas y mejora la productividad en comparación con el uso de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060877"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>SDK de Razor de ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Información general

[!INCLUDE[](~/includes/2.1-SDK.md)] incluye el SDK de MSBuild `Microsoft.NET.Sdk.Razor` (SDK de Razor). El SDK de Razor:

::: moniker range=">= aspnetcore-3.0"

* Es necesario para compilar, empaquetar y publicar proyectos que contengan archivos [Razor](xref:mvc/views/razor) para proyectos de [Blazor](xref:blazor/index) o basados en ASP.NET Core MVC.
* Incluye un conjunto de destinos, propiedades y elementos predefinidos que permiten personalizar la compilación de archivos de Razor ( *.cshtml* o *.razor* ).

El SDK de Razor incluye elementos `Content` con atributos `Include` establecidos en los patrones de uso de comodines `**\*.cshtml` y `**\*.razor`. Los archivos coincidentes se publican.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Normaliza la experiencia de creación, empaquetado y publicación de proyectos que contienen archivos de [Razor](xref:mvc/views/razor) relativos a proyectos basados en ASP.NET Core MVC.
* Incluye un conjunto de destinos, propiedades y elementos predefinidos que permiten personalizar la compilación de archivos de Razor.

El SDK de Razor incluye un elemento `Content` con un atributo `Include` establecido en el patrón de comodines `**\*.cshtml`. Los archivos coincidentes se publican.

::: moniker-end

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Uso del SDK de Razor

En la mayoría de las aplicaciones web no es necesario hacer referencia de forma explícita al SDK de Razor.

::: moniker range=">= aspnetcore-3.0"

Para usar el SDK de Razor a fin de compilar bibliotecas de clases que contengan vistas de Razor o Razor Pages, se recomienda comenzar con la plantilla de proyecto de la biblioteca de clases de Razor (RCL). Una RCL que se usa para compilar archivos ( *.razor* ) de Blazor requiere mínimamente una referencia al paquete [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components). Una RCL que se usa para compilar vistas o páginas de Razor (archivos *.cshtml* ) requiere mínimamente que el destino sea `netcoreapp3.0` o una versión posterior, y tener un elemento `FrameworkReference` al metapaquete [Microsoft.AspNetCore.app](xref:fundamentals/metapackage-app) en su archivo de proyecto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para usar el SDK de Razor para compilar bibliotecas de clases que contengan vistas de Razor o Razor Pages:

* Use `Microsoft.NET.Sdk.Razor` en lugar de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, suele ser necesaria una referencia de paquete a `Microsoft.AspNetCore.Mvc` para recibir dependencias adicionales necesarias para generar y compilar Razor Pages y vistas de Razor. Como mínimo, el proyecto debe agregar referencias de paquete a:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  El paquete `Microsoft.AspNetCore.Razor.Design` proporciona las tareas y los destinos de compilación de Razor para el proyecto.

  Los paquetes anteriores están incluidos en `Microsoft.AspNetCore.Mvc`. En el siguiente marcado se muestra un archivo de proyecto en el que se usa el SDK de Razor para crear archivos de Razor para una aplicación Razor Pages de ASP.NET Core:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Los paquetes `Microsoft.AspNetCore.Razor.Design` y `Microsoft.AspNetCore.Mvc.Razor.Extensions` están incluidos en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Pero la referencia de paquete `Microsoft.AspNetCore.App` sin versión proporciona un metapaquete a la aplicación que no incluye la versión más reciente de `Microsoft.AspNetCore.Razor.Design`. Los proyectos deben hacer referencia a una versión coherente de `Microsoft.AspNetCore.Razor.Design` (o `Microsoft.AspNetCore.Mvc`), de modo que se incluyan las últimas correcciones de tiempo de compilación de Razor. Para más información, consulte [este problema de GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propiedades

Las siguientes propiedades controlan el comportamiento del SDK de Razor como parte de la generación de un proyecto:

* `RazorCompileOnBuild`: si es `true`, compila y emite el ensamblado de Razor como parte de la generación del proyecto. Tiene como valor predeterminado `true`.
* `RazorCompileOnPublish`: si es `true`, compila y emite el ensamblado de Razor como parte de la publicación del proyecto. Tiene como valor predeterminado `true`.

Para configurar las entradas y la salida del SDK de Razor se usan las propiedades y los elementos de la tabla siguiente.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partir de ASP.NET Core 3.0, las vistas MVC o Razor Pages no se proporcionan de forma predeterminada si las propiedades `RazorCompileOnBuild` o `RazorCompileOnPublish` de MSBuild del archivo de proyecto están deshabilitadas. Las aplicaciones deben agregar una referencia explícita al paquete [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) si la aplicación se basa en la compilación en tiempo de ejecución para procesar los archivos *.cshtml*.

::: moniker-end

| Elementos | Descripción |
| ----- | ----------- |
| `RazorGenerate` | Elementos (archivos *.cshtml* ) que son entradas para la generación de código. |
| `RazorComponent` | Elementos (archivos *.razor* que son entradas para la generación de código de componentes de Razor. |
| `RazorCompile` | Elementos (archivos *.cs* que son entradas de los destinos de compilación deRazor. Use este objeto `ItemGroup` para especificar otros archivos que se compilen en el ensamblado de Razor. |
| `RazorTargetAssemblyAttribute` | Elementos que se usan para generar atributos de código para el ensamblado de Razor. Por ejemplo:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementos que se agregan como recursos incrustados al ensamblado de Razor generado. |

::: moniker range=">= aspnetcore-3.0"

| Propiedad. | Descripción |
| -------- | ----------- |
| `RazorTargetName` | Nombre de archivo (sin extensión) del ensamblado generado por Razor. |
| `RazorOutputPath` | Directorio de salida de Razor. |
| `RazorCompileToolset` | Se usa para determinar el conjunto de herramientas que se va a emplear para compilar el ensamblado de Razor. Valores válidos son `Implicit`, `RazorSDK` y `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | El valor predeterminado es `true`. Si es `true`, incluye archivos *web.config* , *.json* y *.cshtml* como contenido en el proyecto. Si la referencia se realizar a través de `Microsoft.NET.Sdk.Web`, también se incluyen todos los archivos bajo *wwwroot* y los archivos de configuración. |
| `EnableDefaultRazorGenerateItems` | Si es `true`, incluye los archivos *.cshtml* de los elementos `Content` en elementos `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Si es `true`, genera un archivo *.cs* que contiene los atributos especificados por `RazorAssemblyAttribute` e incluye el archivo en la salida de compilación. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si es `true`, agrega a `RazorAssemblyAttribute` un conjunto predeterminado de atributos de ensamblado. |
| `CopyRazorGenerateFilesToPublishDirectory` | Si es `true`, copia los archivos ( *.cshtml* ) de los elementos `RazorGenerate` en el directorio de publicación. Normalmente, los archivos de Razor no son necesarios en una aplicación publicada si participan en la compilación en el momento de la compilación o la publicación. Tiene como valor predeterminado `false`. |
| `PreserveCompilationReferences` | Si es `true`, copia los elementos de referencia del ensamblado en el directorio de publicación. Normalmente, los ensamblados de referencia no son necesarios en una aplicación publicada si la compilación de Razor tiene lugar en el momento de la compilación o la publicación. Establézcalo en `true` si la aplicación publicada requiere compilación en tiempo de ejecución. Por ejemplo, establezca el valor en `true` si la aplicación modifica archivos *.cshtml* en tiempo de ejecución o usa vistas insertadas. Tiene como valor predeterminado `false`. |
| `IncludeRazorContentInPack` | Si es `true`, todos los elementos de contenido de Razor (archivos *.cshtml* ) se marcan para incluirse en el paquete NuGet generado. Tiene como valor predeterminado `false`. |
| `EmbedRazorGenerateSources` | Si es `true`, agrega los elementos RazorGenerate ( *.cshtml* ) como archivos incrustados al ensamblado de Razor generado. Tiene como valor predeterminado `false`. |
| `UseRazorBuildServer` | Si es `true`, usa un proceso de servidor de compilación persistente para descargar el trabajo de generación de código. El valor predeterminado es `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Si es `true`, el SDK genera atributos adicionales usados por MVC en tiempo de ejecución para realizar la detección de elementos de la aplicación. |
| `DefaultWebContentItemExcludes` | Un patrón global para elementos que se van a excluir del grupo de elementos `Content` en proyectos destinados a Internet o el SDK de Razor. |
| `ExcludeConfigFilesFromBuildOutput` | Si es `true`, los archivos *.config* y *.json* no se copian en el directorio de salida de la compilación. |
| `AddRazorSupportForMvc` | Si es `true`, configura el SDK de Razor para agregar compatibilidad con la configuración de MVC necesaria al compilar aplicaciones que contienen vistas de MVC o Razor Pages. Esta propiedad se establece de forma implícita para proyectos de .NET Core 3.0 o versiones posteriores que tienen como destino el SDK Web |
| `RazorLangVersion` | Versión del lenguaje Razor de destino. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propiedad. | Descripción |
| -------- | ----------- |
| `RazorTargetName` | Nombre de archivo (sin extensión) del ensamblado generado por Razor. |
| `RazorOutputPath` | Directorio de salida de Razor. |
| `RazorCompileToolset` | Se usa para determinar el conjunto de herramientas que se va a emplear para compilar el ensamblado de Razor. Valores válidos son `Implicit`, `RazorSDK` y `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | El valor predeterminado es `true`. Si es `true`, incluye archivos *web.config* , *.json* y *.cshtml* como contenido en el proyecto. Si la referencia se realizar a través de `Microsoft.NET.Sdk.Web`, también se incluyen todos los archivos bajo *wwwroot* y los archivos de configuración. |
| `EnableDefaultRazorGenerateItems` | Si es `true`, incluye los archivos *.cshtml* de los elementos `Content` en elementos `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Si es `true`, genera un archivo *.cs* que contiene los atributos especificados por `RazorAssemblyAttribute` e incluye el archivo en la salida de compilación. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si es `true`, agrega a `RazorAssemblyAttribute` un conjunto predeterminado de atributos de ensamblado. |
| `CopyRazorGenerateFilesToPublishDirectory` | Si es `true`, copia los archivos ( *.cshtml* ) de los elementos `RazorGenerate` en el directorio de publicación. Normalmente, los archivos de Razor no son necesarios en una aplicación publicada si participan en la compilación en el momento de la compilación o la publicación. Tiene como valor predeterminado `false`. |
| `CopyRefAssembliesToPublishDirectory` | Si es `true`, copia los elementos de referencia del ensamblado en el directorio de publicación. Normalmente, los ensamblados de referencia no son necesarios en una aplicación publicada si la compilación de Razor tiene lugar en el momento de la compilación o la publicación. Establézcalo en `true` si la aplicación publicada requiere compilación en tiempo de ejecución. Por ejemplo, establezca el valor en `true` si la aplicación modifica archivos *.cshtml* en tiempo de ejecución o usa vistas insertadas. Tiene como valor predeterminado `false`. |
| `IncludeRazorContentInPack` | Si es `true`, todos los elementos de contenido de Razor (archivos *.cshtml* ) se marcan para incluirse en el paquete NuGet generado. Tiene como valor predeterminado `false`. |
| `EmbedRazorGenerateSources` | Si es `true`, agrega los elementos RazorGenerate ( *.cshtml* ) como archivos incrustados al ensamblado de Razor generado. Tiene como valor predeterminado `false`. |
| `UseRazorBuildServer` | Si es `true`, usa un proceso de servidor de compilación persistente para descargar el trabajo de generación de código. El valor predeterminado es `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Si es `true`, el SDK genera atributos adicionales usados por MVC en tiempo de ejecución para realizar la detección de elementos de la aplicación. |
| `DefaultWebContentItemExcludes` | Un patrón global para elementos que se van a excluir del grupo de elementos `Content` en proyectos destinados a Internet o el SDK de Razor. |
| `ExcludeConfigFilesFromBuildOutput` | Si es `true`, los archivos *.config* y *.json* no se copian en el directorio de salida de la compilación. |
| `AddRazorSupportForMvc` | Si es `true`, configura el SDK de Razor para agregar compatibilidad con la configuración de MVC necesaria al compilar aplicaciones que contienen vistas de MVC o Razor Pages. Esta propiedad se establece de forma implícita para proyectos de .NET Core 3.0 o versiones posteriores que tienen como destino el SDK Web |
| `RazorLangVersion` | Versión del lenguaje Razor de destino. |

::: moniker-end

Para más información sobre las propiedades, consulte [Propiedades de MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

El SDK de Razor define dos destinos principales:

* `RazorGenerate`: el código genera archivos *.cs* a partir de elementos `RazorGenerate`. Use la propiedad `RazorGenerateDependsOn` para especificar más destinos que se puedan ejecutar antes o después de este destino.
* `RazorCompile`: compila los archivos *.cs* generados en un ensamblado de Razor. Use `RazorCompileDependsOn` para especificar más destinos que se puedan ejecutar antes o después de este destino.
* `RazorComponentGenerate`: el código genera archivos *.cs* para elementos `RazorComponent`. Use la propiedad `RazorComponentGenerateDependsOn` para especificar más destinos que se puedan ejecutar antes o después de este destino.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Compilación en tiempo de ejecución de vistas de Razor

* De forma predeterminada, el SDK de Razor no publica los ensamblados de referencia necesarios para realizar una compilación en tiempo de ejecución. Como consecuencia, se producen errores de compilación cuando el modelo de aplicación se basa en la compilación en tiempo de ejecución; por ejemplo, la aplicación usa vistas incrustadas o cambia vistas tras haber sido publicada. Establezca `CopyRefAssembliesToPublishDirectory` en `true` para seguir publicando ensamblados de referencia.

* En el caso de una aplicación web, asegúrese de que se destina al SDK `Microsoft.NET.Sdk.Web`.

## <a name="no-locrazor-language-version"></a>Versión del lenguaje Razor

Si el destino es el SDK `Microsoft.NET.Sdk.Web`, la versión del lenguaje Razor se deduce de la versión del marco de destino de la aplicación. En los proyectos que tienen como destino el SDK de `Microsoft.NET.Sdk.Razor` o en el caso excepcional de que la aplicación requiera una versión del lenguaje Razor diferente al valor deducido, se puede configurar una versión si se establece la propiedad `<RazorLangVersion>` en el archivo de proyecto de la aplicación:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

La versión del lenguaje Razor se integra estrechamente con la versión del runtime para la que se ha compilado. No se admite seleccionar como destino una versión del lenguaje que no está diseñada para el entorno de ejecución y es probable que genere errores de compilación.

## <a name="additional-resources"></a>Recursos adicionales

* [Adiciones al formato csproj para .NET Core](/dotnet/core/tools/csproj)
* [Elementos comunes de proyectos de MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
