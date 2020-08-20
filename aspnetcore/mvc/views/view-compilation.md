---
title: Razor compilación de archivos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo Razor se produce la compilación de archivos en una aplicación ASP.net Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d76eff93d5c7c53b57136e5183e1ca5287dec81
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631126"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor compilación de archivos en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razorlos archivos con una extensión *. cshtml* se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk). La compilación en tiempo de ejecución puede habilitarse opcionalmente mediante la configuración del proyecto.

## <a name="no-locrazor-compilation"></a>Razor previa

RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y en tiempo de publicación Razor . Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite Razor actualizar los archivos si se modifican.

## <a name="enable-runtime-compilation-at-project-creation"></a>Habilitar compilación en tiempo de ejecución al crear el proyecto

Las Razor plantillas de proyecto pages y MVC incluyen una opción para habilitar la compilación en tiempo de ejecución cuando se crea el proyecto. Esta opción se admite en ASP.NET Core 3,1 y versiones posteriores.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el cuadro de diálogo **crear una nueva aplicación Web de ASP.net Core** :

1. Seleccione la plantilla de **proyecto aplicación web o** **aplicación web (controlador de vista de modelos)** .
1. Active la casilla **habilitar Razor compilación en tiempo de ejecución** .

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Use la `-rrc` `--razor-runtime-compilation` opción de plantilla o. Por ejemplo, el siguiente comando crea un nuevo Razor proyecto de páginas con la compilación en tiempo de ejecución habilitada:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Habilitar la compilación en tiempo de ejecución en un proyecto existente

Para habilitar la compilación en tiempo de ejecución para todos los entornos de un proyecto existente:

1. Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Paquete NuGet de RuntimeCompilation.
1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Por ejemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Habilitar la compilación en tiempo de ejecución de forma condicional en un proyecto existente

La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local. Este modo de habilitación condicional garantiza que la salida publicada:

* Usa vistas precompiladas.
* No habilita monitores de archivos en producción.

Para habilitar la compilación en tiempo de ejecución solo en el entorno de desarrollo:

1. Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Paquete NuGet de RuntimeCompilation.
1. Modifique la sección de perfil `environmentVariables` de inicio de *launchSettings.jsen*:
    * Compruebe `ASPNETCORE_ENVIRONMENT` que está establecido en `"Development"` .
    * Establezca `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` en `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

En el ejemplo siguiente, la compilación en tiempo de ejecución está habilitada en el entorno de desarrollo para los `IIS Express` `RazorPagesApp` perfiles de inicio y:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

No se necesitan cambios en el código en la `Startup` clase del proyecto. En tiempo de ejecución, ASP.NET Core busca un [atributo HostingStartup de nivel de ensamblado](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) en `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . El `HostingStartup` atributo especifica el código de inicio de la aplicación que se va a ejecutar. Ese código de inicio habilita la compilación en tiempo de ejecución.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Habilitar la compilación en tiempo de ejecución para una Razor biblioteca de clases

Considere un escenario en el que un Razor proyecto de páginas hace referencia a una [ Razor biblioteca de clases (RCL)](xref:razor-pages/ui-class) denominada *MyClassLib*. RCL contiene un archivo *_Layout. cshtml* que consumen todos los proyectos de MVC y Pages de su equipo Razor . Quiere habilitar la compilación en tiempo de ejecución para el archivo *_Layout. cshtml* en esa RCL. Realice los cambios siguientes en el Razor proyecto de páginas:

1. Habilite la compilación en tiempo de ejecución con las instrucciones [para habilitar de forma condicional la compilación en tiempo de ejecución en un proyecto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Configurar las opciones de compilación en tiempo de ejecución en `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    En el código anterior, se construye una ruta de acceso absoluta al RCL de *MyClassLib* . La [API de PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) se usa para buscar directorios y archivos en esa ruta de acceso absoluta. Por último, la `PhysicalFileProvider` instancia se agrega a una colección de proveedores de archivos, que permite el acceso a los archivos *. CSHTML* de RCL.

## <a name="additional-resources"></a>Recursos adicionales

* Propiedades [ Razor CompileOnBuild y Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razorlos archivos con una extensión *. cshtml* se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk). La compilación en tiempo de ejecución se puede habilitar opcionalmente mediante la configuración de la aplicación

## <a name="no-locrazor-compilation"></a>Razor previa

RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y en tiempo de publicación Razor . Cuando está habilitada, la compilación en tiempo de ejecución complementa la compilación en tiempo de compilación, lo que permite Razor actualizar los archivos si se modifican.

## <a name="runtime-compilation"></a>Compilación en tiempo de ejecución

Para habilitar la compilación en tiempo de ejecución para todos los entornos y modos de configuración:

1. Instale [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Paquete NuGet de RuntimeCompilation.

1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>. Por ejemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Habilitación condicional de la compilación en tiempo de ejecución

La compilación en tiempo de ejecución se puede habilitar para que solo esté disponible para el desarrollo local. Este modo de habilitación condicional garantiza que la salida publicada:

* Usa vistas precompiladas.
* Tiene un tamaño inferior.
* No habilita monitores de archivos en producción.

Para habilitar la compilación en tiempo de ejecución basada en el modo de configuración y el entorno, deberá:

1. Haga referencia condicionalmente a [Microsoft. AspNetCore. Mvc. Razor . Paquete RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basado en el `Configuration` valor activo:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Actualizar el método `Startup.ConfigureServices` del proyecto para incluir una llamada a `AddRazorRuntimeCompilation`. Ejecute `AddRazorRuntimeCompilation` de manera condicional de modo que solo se ejecute en modo de depuración cuando la variable `ASPNETCORE_ENVIRONMENT` esté establecida en `Development`:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* Propiedades [ Razor CompileOnBuild y Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Vea el [ejemplo de compilación en tiempo de ejecución en github](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obtener un ejemplo que muestra cómo se realiza el trabajo de compilación en tiempo de ejecución entre proyectos.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un Razor archivo se compila en tiempo de ejecución, cuando Razor se invoca la vista de MVC o la página asociada. Razorlos archivos se compilan en el momento de la compilación y de la publicación mediante el [ Razor SDK](xref:razor-pages/sdk).

## <a name="no-locrazor-compilation"></a>Razor previa

RazorEl SDK habilita de forma predeterminada la compilación de archivos en tiempo de compilación y de publicación Razor . La edición Razor de archivos tras su actualización se admite en tiempo de compilación. De forma predeterminada, solo se implementan en la aplicación los archivos *Views.dll* compilados y los archivos *. cshtml* o los ensamblados de referencia necesarios para compilar Razor archivos.

> [!IMPORTANT]
> La herramienta de precompilación está en desuso y se eliminará en ASP.NET Core 3.0. Se recomienda migrar al [ Razor SDK](xref:razor-pages/sdk).
>
> El Razor SDK solo es efectivo cuando no hay ninguna propiedad específica de precompilación establecida en el archivo de proyecto. Por ejemplo, al establecer la propiedad del archivo *. csproj* `MvcRazorCompileOnPublish` en, se `true` deshabilita el Razor SDK.

## <a name="runtime-compilation"></a>Compilación en tiempo de ejecución

La compilación en tiempo de compilación se complementa por la compilación en tiempo de ejecución de Razor los archivos. ASP.NET Core MVC volverá a compilar Razor los archivos cuando cambie el contenido de un archivo *. cshtml* .

## <a name="additional-resources"></a>Recursos adicionales

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
