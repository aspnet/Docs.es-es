---
title: Desarrollar aplicaciones ASP.NET Core con un monitor de archivos
author: rick-anderson
description: Este tutorial muestra cómo instalar y usar la herramienta de monitor de archivos (dotnet watch) de la CLI de .NET Core en una aplicación de ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 0c81dc2fd83cb9327dad46dab840d4352de89baf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409038"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Desarrollar aplicaciones ASP.NET Core con un monitor de archivos

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[dotnet watch](https://www.nuget.org/packages/dotnet-watch) es una herramienta que ejecuta un comando de la [CLI de .NET Core](/dotnet/core/tools) cuando se modifican los archivos de código fuente. Por ejemplo, un cambio en un archivo puede desencadenar una compilación, una ejecución de prueba o una implementación.

En este tutorial usaremos una API web existente con dos puntos de conexión: uno que devuelve una suma y otro que devuelve un producto. El método Product contiene un error, que se ha corregido en este tutorial.

Descargue la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Consta de dos proyectos: *WebApp* (API web de ASP.NET Core) y *WebAppTests* (pruebas unitarias para la API web).

En un shell de comandos, desplácese hasta la carpeta *WebApp*. Ejecute el siguiente comando:

```dotnetcli
dotnet run
```

> [!NOTE]
> Puede usar `dotnet run --project <PROJECT>` para especificar un proyecto para que se ejecute. Por ejemplo, al ejecutar `dotnet run --project WebApp` desde la raíz de la aplicación de ejemplo, también se ejecutará el proyecto *WebApp*.

La salida de la consola muestra mensajes similares al siguiente (indicando que la aplicación se ejecuta y espera solicitudes):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

En un explorador web, vaya a `http://localhost:<port number>/api/math/sum?a=4&b=5`. Debería ver el resultado de `9`.

Navegue a la API del producto (`http://localhost:<port number>/api/math/product?a=4&b=5`). Devuelve `9`, no `20` tal como se esperaría. Ese problema se corregirá más adelante en el tutorial.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Agregar `dotnet watch` a un proyecto

La herramienta de monitor de archivos `dotnet watch` se incluye con la versión 2.1.300 del SDK de .NET Core. Si se usa una versión anterior del SDK de .NET Core, será necesario realizar los siguientes pasos.

1. Agregue una referencia de paquete `Microsoft.DotNet.Watcher.Tools` al archivo *.csproj*:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Instale el paquete `Microsoft.DotNet.Watcher.Tools` mediante la ejecución del comando siguiente:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Ejecutar los comandos de la CLI de .NET Core con `dotnet watch`

Cualquier [comando de la CLI de .NET Core](/dotnet/core/tools#cli-commands) se puede ejecutar con `dotnet watch`. Por ejemplo:

| Comando | Comando con watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Ejecute `dotnet watch run` en la carpeta *WebApp*. La salida de la consola indica que se ha iniciado `watch`.

> [!NOTE]
> Puede usar `dotnet watch --project <PROJECT>` para especificar un proyecto para verlo. Por ejemplo, al ejecutar `dotnet watch --project WebApp run` desde la raíz de la aplicación de ejemplo, también se ejecutará y se verá el proyecto *WebApp*.

## <a name="make-changes-with-dotnet-watch"></a>Realizar cambios con `dotnet watch`

Asegúrese de que `dotnet watch` se está ejecutando.

Corrija el error en el método `Product` de *MathController.cs* para que devuelva el producto y no la suma:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Guarde el archivo. La salida de la consola muestra que `dotnet watch` ha detectado un cambio de archivo y ha reiniciado la aplicación.

Compruebe que `http://localhost:<port number>/api/math/product?a=4&b=5` devuelve el resultado correcto.

## <a name="run-tests-using-dotnet-watch"></a>Ejecutar pruebas con `dotnet watch`

1. Vuelva a cambiar el método `Product` de *MathController.cs* para devolver la suma. Guarde el archivo.
1. En un shell de comandos, desplácese hasta la carpeta *WebAppTests*.
1. Ejecute [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Ejecute `dotnet watch test`. La salida que indica que se ha producido un error en una prueba y que el monitor espera cambios de archivos:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Corrija el código del método `Product` para que devuelva el producto. Guarde el archivo.

`dotnet watch` detecta el cambio de archivo y vuelve a ejecutar las pruebas. La salida de la consola indica que se han superado las pruebas.

## <a name="customize-files-list-to-watch"></a>Personalizar la lista de archivos que inspeccionar

`dotnet-watch` realiza un seguimiento de forma predeterminada de todos los archivos que coincidan con los siguientes patrones globales:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Se pueden agregar más elementos a la lista de control inspección editando el archivo *.csproj*. Los elementos se pueden especificar individualmente o usando patrones globales.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Descartar archivos de la inspección

`dotnet-watch` se puede configurar para pasar por alto su configuración predeterminada. Para omitir archivos concretos, agregue el atributo `Watch="false"` a la definición de un elemento en el archivo *.csproj*:

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>Proyectos de inspección personalizados

`dotnet-watch` no queda restringido exclusivamente a proyectos de C#, sino que se pueden crear proyectos de inspección personalizados para controlar distintos escenarios. Veamos el siguiente diseño de proyecto:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Si el objetivo es inspeccionar los dos proyectos, cree un archivo de proyecto personalizado configurado para supervisar ambos proyectos:

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

Para empezar a inspeccionar archivos en ambos proyectos, cambie a la carpeta *test*. Ejecute el siguiente comando:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest se ejecuta cuando un archivo de cualquiera de los proyectos de prueba cambie.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` en GitHub

`dotnet-watch` forma parte del repositorio [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch) de GitHub.
