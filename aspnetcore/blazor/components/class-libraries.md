---
title: Bibliotecas de clases de componentes de Razor de ASP.NET Core
author: guardrex
description: Descubra cómo se pueden incluir componentes en aplicaciones de Blazor desde una biblioteca de componentes externa.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 24a5b93a18cfe36c50d9739ba56d12aca41615c0
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570164"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>Bibliotecas de clases de componentes de Razor de ASP.NET Core

Por [Simon Timms](https://github.com/stimms)

Los componentes se pueden compartir entre proyectos en una [biblioteca de clases de Razor (RCL)](xref:razor-pages/ui-class). Se puede incluir una *biblioteca de clases de componentes de Razor* desde:

* Otro proyecto de la solución.
* Un paquete NuGet.
* Una biblioteca de .NET a la que se hace referencia.

Del mismo modo que los componentes son tipos normales de .NET, los componentes proporcionados por una RCL son ensamblados .NET normales.

## <a name="create-an-rcl"></a>Creación de una RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cree un nuevo proyecto.
1. Seleccione **Biblioteca de clases de Razor** . Seleccione **Siguiente**.
1. En el cuadro de diálogo **Crear una biblioteca de clases de Razor** , seleccione **Crear**.
1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. En los ejemplos de este tema se usa el nombre de proyecto `ComponentLibrary`. Seleccione **Crear**.
1. Agregue la RCL a una solución:
   1. Haga clic con el botón derecho en la solución. Seleccione **Agregar** > **Proyecto existente**.
   1. Navegue hasta el archivo del proyecto de la RCL.
   1. Seleccione el archivo de proyecto de la RCL (`.csproj`).
1. Agregue una referencia a la RCL desde la aplicación:
   1. Haga clic con el botón derecho en el proyecto de la aplicación. Seleccione **Agregar** > **Referencia**.
   1. Seleccione el proyecto de la RCL. Seleccione **Aceptar**.

> [!NOTE]
> Si la casilla **Páginas y vistas de soporte técnico** está activada al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Agregue manualmente el archivo a la raíz del proyecto generado.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

1. Use la plantilla **Biblioteca de clases de Razor** (`razorclasslib`) con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos. En el ejemplo siguiente, se crea una RCL llamada `ComponentLibrary`. La carpeta que contiene `ComponentLibrary` se crea automáticamente cuando se ejecuta el comando:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Si se usa el modificador `-s|--support-pages-and-views` al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Agregue manualmente el archivo a la raíz del proyecto generado.

1. Para agregar la biblioteca a un proyecto existente, use el comando [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) en un shell de comandos. En el siguiente ejemplo, se agrega la RCL a la aplicación. Ejecute el siguiente comando desde la carpeta de proyecto de la aplicación con la ruta de acceso a la biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumo de un componente de biblioteca

Para consumir los componentes definidos en una biblioteca de otro proyecto, siga cualquiera de los métodos siguientes:

* Use el nombre de tipo completo con el espacio de nombres.
* Use la directiva [`@using`](xref:mvc/views/razor#using) de Razor. Los componentes individuales se pueden agregar por nombre.

En los ejemplos siguientes, `ComponentLibrary` es una biblioteca de componentes que contiene el componente `Component1` (`Component1.razor`). El componente `Component1` es un componente de ejemplo que la plantilla de proyecto de RCL agrega automáticamente cuando se crea la biblioteca.

Haga referencia al componente `Component1` mediante su espacio de nombres:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Como alternativa, coloque la biblioteca en el ámbito con una directiva [`@using`](xref:mvc/views/razor#using) y use el componente sin su espacio de nombres:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

De manera opcional, incluya la directiva `@using ComponentLibrary` en el archivo de nivel superior `_Import.razor` a fin de que los componentes de la biblioteca estén disponibles para un proyecto completo. Agregue la directiva a un archivo `_Import.razor` en cualquier nivel para aplicar el espacio de nombres a un solo componente o a un conjunto de componentes dentro de una carpeta.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Para proporcionar la clase CSS `my-component` de `Component1`, establezca un vínculo con la hoja de estilo de la biblioteca en el archivo `wwwroot/index.html` (Blazor WebAssembly) o el archivo `Pages/_Host.cshtml` (Blazor Server) de la aplicación:

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Creación de una biblioteca de clases de componentes de Razor con recursos estáticos

Una RCL puede incluir recursos estáticos. Los recursos estáticos están disponibles para cualquier aplicación que use la biblioteca. Para obtener más información, vea <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Proporcionar componentes y recursos estáticos a varias aplicaciones de Blazor hospedadas

Para obtener más información, vea <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Analizador de compatibilidad con el explorador de Blazor WebAssembly

Las aplicaciones Blazor WebAssembly tienen como destino el área expuesta de la API de .NET completa, pero no todas las API de .NET son compatibles con WebAssembly, debido a las restricciones de espacio aislado del explorador. Las API no compatibles inician la excepción <xref:System.PlatformNotSupportedException> cuando se ejecutan en WebAssembly. Un analizador de compatibilidad de plataforma advierte al desarrollador cuando la aplicación usa API que no son compatibles con las plataformas de destino de la aplicación. En el caso de las aplicaciones Blazor WebAssembly, esto significa comprobar que las API se admiten en los exploradores. La anotación de API de .NET Framework para el analizador de compatibilidad es un proceso constante, por lo que no todas las API de .NET Framework están anotadas actualmente.

Los proyectos de biblioteca de clases de Blazor WebAssembly y Razor habilitan *automáticamente* las comprobaciones de compatibilidad del explorador agregando `browser` como plataforma compatible con el elemento `SupportedPlatform` de MSBuild. Los desarrolladores de bibliotecas pueden agregar manualmente el elemento `SupportedPlatform` al archivo de proyecto de una biblioteca para habilitar la característica:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Al crear una biblioteca, especifique `browser` en <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> para indicar que los exploradores no admiten una API determinada:

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Para más información, vea [Anotación de API como no compatibles en plataformas específicas (repositorio de GitHub dotnet/designs)](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Aislamiento de JavaScript y referencias a objetos en Blazor

Blazor permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar. El aislamiento de JavaScript reporta las siguientes ventajas:

* El código JavaScript importado no contamina el espacio de nombres global.
* No es necesario que los consumidores de la biblioteca y los componentes importen manualmente el código JavaScript relacionado.

Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Compilación, empaquetado y envío de bibliotecas a NuGet

Dado que las bibliotecas de componentes son bibliotecas estándar de .NET, se empaquetan y se envían a NuGet de la misma manera que cualquier otra biblioteca. El empaquetado se realiza mediante el comando [`dotnet pack`](/dotnet/core/tools/dotnet-pack) en un shell de comandos:

```dotnetcli
dotnet pack
```

Cargue el paquete en NuGet usando el comando [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) en un shell de comandos.

## <a name="additional-resources"></a>Recursos adicionales

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Adición de un archivo de configuración del recortador de lenguaje intermedio (IL) XML a una biblioteca](xref:blazor/host-and-deploy/configure-trimmer)
* [Compatibilidad de aislamiento de CSS con bibliotecas de clases Razor](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Agregar un archivo de configuración del enlazador de lenguaje intermedio (IL) XML a una biblioteca](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
