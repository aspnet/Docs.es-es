---
title: Configuración del enlazador de ASP.NET Core Blazor
author: guardrex
description: Aprenda a controlar al enlazador de lenguaje intermedio (IL) al crear una aplicación Blazor.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: af3c059e7192d6b0d2b0a902b6e3a6121fdf6709
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395037"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configuración del enlazador de ASP.NET Core Blazor

Blazor WebAssembly realiza la vinculación de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilación para reducir el IL innecesario de los ensamblados de salida de la aplicación. El enlazador está deshabilitado durante la compilación en la configuración Debug. Las aplicaciones deben compilarse en la configuración Release para habilitar el enlazador. Se recomienda compilar en Release cuando se implementen las aplicaciones Blazor WebAssembly. 

La vinculación de una aplicación optimiza el tamaño, pero puede tener efectos perjudiciales. Las aplicaciones que usan la reflexión o características dinámicas relacionadas pueden verse interrumpidas cuando se reduzcan porque el enlazador no conoce este comportamiento dinámico y no puede determinar, en general, qué tipos son necesarios para la reflexión en el entorno de ejecución. Para reducir estas aplicaciones, se debe informar al vinculador de los tipos necesarios para la reflexión en el código y en los paquetes o marcos de trabajo de los que depende la aplicación.

Para asegurarse de que la aplicación reducida funciona correctamente una vez implementada, es importante probar las compilaciones de Release de la aplicación con frecuencia durante el desarrollo.

La vinculación de las aplicaciones de Blazor se puede configurar con estas características de MSBuild:

* Configuración de la vinculación global con una [propiedad de MSBuild](#control-linking-with-an-msbuild-property).
* Control de la vinculación por cada ensamblado con un [archivo de configuración](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Control de la vinculación con una propiedad de MSBuild

La vinculación se habilita cuando una aplicación se compila en la configuración `Release`. Para cambiar esto, configure la propiedad `BlazorWebAssemblyEnableLinking` de MSBuild en el archivo del proyecto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Control de la vinculación con un archivo de configuración

Control de la vinculación por cada ensamblado al proporcionar un archivo de configuración XML y especificar el archivo como un elemento MSBuild en el archivo de proyecto:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Para obtener más información y ejemplos, vea [Formatos de datos (repositorio mono/linker de GitHub)](https://github.com/mono/linker/blob/main/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Adición de un archivo de configuración del enlazador XML a una biblioteca

Para configurar el enlazador para una biblioteca específica, agregue un archivo de configuración de enlazador XML a la biblioteca como un recurso incrustado. El recurso incrustado debe tener el mismo nombre que el ensamblado.

En el ejemplo siguiente, el archivo `LinkerConfig.xml` se especifica como un recurso incrustado que tiene el mismo nombre que el ensamblado de la biblioteca:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configuración del enlazador para la internacionalización

De forma predeterminada, la configuración del enlazador de Blazor para aplicaciones Blazor WebAssembly quita información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita. Al quitar estos ensamblados se minimiza el tamaño de la aplicación.

Para controlar qué ensamblados de I18N se conservan, establezca la propiedad `<BlazorWebAssemblyI18NAssemblies>` de MSBuild en el archivo de proyecto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Valor de región     | Ensamblado de región de Mono    |
| ---------------- | ----------------------- |
| `all`            | Todos los ensamblados incluidos |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none` (valor predeterminado) | None                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

Use una coma para separar varios valores (por ejemplo, `mideast,west`).

Para más información, vea [I18N: biblioteca del marco de internacionalización Pnetlib (repositorio de GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
