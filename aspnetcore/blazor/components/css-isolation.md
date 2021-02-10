---
title: Aislamiento de CSS de Blazor de ASP.NET Core
author: daveabrock
description: Conozca que el aislamiento de CSS permite aplicar ámbito de CSS a los componentes, lo que puede simplificar CSS y evitar conflictos con otros componentes o bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529987"
---
# <a name="aspnet-core-blazor-css-isolation"></a>Aislamiento de CSS de Blazor de ASP.NET Core

De [Dave Brock](https://twitter.com/daveabrock)

El aislamiento de CSS simplifica la superficie de CSS de una aplicación al evitar dependencias en estilos globales y ayuda a evitar conflictos de estilo entre componentes y bibliotecas.

## <a name="enable-css-isolation"></a>Habilitación del aislamiento de CSS 

Para definir estilos específicos de un componente, cree un archivo `.razor.css` cuyo nombre coincida con el del archivo `.razor` del componente en la misma carpeta. El archivo `.razor.css` es un *archivo CSS con ámbito*. 

En un componente `Example` de un archivo `Example.razor`, cree un archivo junto al componente denominado `Example.razor.css`. El archivo `Example.razor.css` debe residir en la misma carpeta que el componente `Example` (`Example.razor`). El nombre base "`Example`" del archivo **no** distingue mayúsculas de minúsculas.

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

**Los estilos definidos en `Example.razor.css` solo se aplican a la salida representada del componente `Example`** . El aislamiento de CSS se aplica a los elementos HTML en el archivo Razor coincidente. Cualquier declaración CSS `h1` definida en otra ubicación de la aplicación no entra en conflicto con los estilos del componente `Example`.

> [!NOTE]
> Para garantizar el aislamiento de estilo en el momento de la unión, no se admite la importación de CSS en bloques de código Razor.

## <a name="css-isolation-bundling"></a>Unión del aislamiento de CSS

El aislamiento de CSS se produce en tiempo de compilación. Durante este proceso, Blazor reescribe los selectores de CSS para que coincidan con el marcado representado por el componente. Estos estilos de CSS reescritos se unen y se generan como un recurso estático en `{PROJECT NAME}.styles.css`, donde el marcador de posición `{PROJECT NAME}` es el nombre del paquete o del producto al que se hace referencia.

De forma predeterminada, se hace referencia a estos archivos estáticos desde la ruta de acceso raíz de la aplicación. En la aplicación, haga referencia al archivo unido mediante la referencia dentro de la etiqueta `<head>` del HTML generado:

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

En el archivo unido, cada componente está asociado a un identificador de ámbito. En cada componente con estilo, se anexa un atributo HTML con el formato `b-<10-character-string>`. El identificador es único y diferente para cada aplicación. En el componente `Counter` representado, Blazor anexa un identificador de ámbito al elemento `h1`:

```html
<h1 b-3xxtam6d07>
```

El archivo `ProjectName.styles.css` usa el identificador de ámbito para agrupar una declaración de estilo con su componente. En el ejemplo siguiente se proporciona el estilo del elemento `<h1>` anterior:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

En tiempo de compilación, se crea un conjunto del proyecto con la convención `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, donde el marcador de posición `{STATIC WEB ASSETS BASE PATH}` es la ruta de acceso base a los recursos web estáticos.

Si se usan otros proyectos, como paquetes NuGet o [bibliotecas de clases de Razor](xref:blazor/components/class-libraries), el archivo unido:

* Hace referencia a los estilos mediante importaciones de CSS.
* No se publica como recurso web estático de la aplicación que consume los estilos.

## <a name="child-component-support"></a>Compatibilidad de componente secundario

De forma predeterminada, el aislamiento de CSS solo se aplica al componente que se asocia con el formato `{COMPONENT NAME}.razor.css`, donde el marcador de posición `{COMPONENT NAME}` suele ser el nombre del componente. Para aplicar cambios a un componente secundario, use el combinador `::deep` con los elementos descendientes del archivo `.razor.css` del componente primario. El combinador `::deep` selecciona los elementos que son *descendientes* del identificador de ámbito generado de un elemento. 

En el ejemplo siguiente se muestra un componente primario denominado `Parent` con un componente secundario denominado `Child`.

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

Actualice la declaración `h1` de `Parent.razor.css` con el combinador `::deep` para indicar que la declaración de estilo de `h1` debe aplicarse al componente primario y a sus elementos secundarios.

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

El estilo de `h1` ahora se aplica a los componentes `Parent` y `Child` sin necesidad de crear un archivo CSS con ámbito independiente para el componente secundario.

El combinador `::deep` solo funciona con elementos descendientes. El marcado siguiente aplica los estilos de `h1` a los componentes según lo esperado. El identificador de ámbito del componente primario se aplica al elemento `div`, por lo que se sabe que el explorador hereda los estilos del componente primario.

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

Sin embargo, si se excluye el elemento `div`, se quita la relación descendiente. En el ejemplo siguiente, el estilo **no** se aplica al componente secundario.

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>Compatibilidad del preprocesador de CSS

Los preprocesadores de CSS son útiles para mejorar el desarrollo de CSS mediante el uso de características como variables, anidamiento, módulos, mixins y herencia. Aunque el aislamiento de CSS no admite de forma nativa preprocesadores de CSS como Sass o Less, la integración de preprocesadores de CSS se realiza sin problemas siempre que se produzca la compilación del preprocesador antes de que Blazor reescriba los selectores de CSS durante el proceso de compilación. Con Visual Studio, por ejemplo, configure la compilación del preprocesador existente como una tarea **Antes de la compilación** en el Explorador del Ejecutor de tareas de Visual Studio.

Muchos paquetes NuGet de terceros, como [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), pueden compilar archivos SASS/SCSS al principio del proceso de compilación antes de que se produzca el aislamiento de CSS y no requieren ninguna configuración adicional.

## <a name="css-isolation-configuration"></a>Configuración del aislamiento de CSS

El aislamiento de CSS está diseñado para poder trabajar de inmediato, pero proporciona configuración para algunos escenarios avanzados, por ejemplo cuando hay dependencias en herramientas o flujos de trabajo existentes.

### <a name="customize-scope-identifier-format"></a>Personalización del formato del identificador de ámbito

De forma predeterminada, los identificadores de ámbito usan el formato `b-<10-character-string>`. Para personalizar el formato de un identificador de ámbito, actualice el archivo del proyecto a un patrón deseado:

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

En el ejemplo anterior, el CSS generado para `Example.Razor.css` cambia su identificador de ámbito de `b-<10-character-string>` a `my-custom-scope-identifier`.

Use identificadores de ámbito para lograr la herencia con archivos CSS de ámbito. En el siguiente ejemplo del archivo del proyecto, un archivo `BaseComponent.razor.css` contiene estilos comunes en todos los componentes. Un archivo `DerivedComponent.razor.css` hereda estos estilos.

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Use el operador comodín (`*`) para compartir los identificadores de ámbito en varios archivos:

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>Cambio de la ruta de acceso base de recursos web estáticos

El archivo `scoped.styles.css` se genera en la raíz de la aplicación. En el archivo del proyecto, use la propiedad `StaticWebAssetBasePath` para cambiar la ruta de acceso predeterminada. En el ejemplo siguiente se coloca el archivo `scoped.styles.css`, y el resto de los recursos de la aplicación, en la ruta de acceso `_content`:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Deshabilitación de la unión automática

Para no usar el modo en que Blazor publica y carga archivos con ámbito en tiempo de ejecución, use la propiedad `DisableScopedCssBundling`. Al usar esta propiedad, otras herramientas o procesos son responsables de tomar los archivos CSS aislados del directorio `obj` y de publicarlos y cargarlos en tiempo de ejecución:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Razor Compatibilidad con la biblioteca de clases (RCL)

Cuando una [Razor biblioteca de clases (RCL)](xref:razor-pages/ui-class) proporciona estilos aislados, el atributo `href` de la etiqueta `<link>` apunta a `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, donde los marcadores de posición son los siguientes:

* `{STATIC WEB ASSET BASE PATH}`: ruta de acceso base del recurso web estático.
* `{ASSEMBLY NAME}`: nombre del ensamblado de la biblioteca de clases.

En el ejemplo siguiente:

* La ruta de acceso base del recurso web estático es `_content/ClassLib`.
* El nombre del ensamblado de la biblioteca de clases es `ClassLib`.

`wwwroot/index.html` (Blazor WebAssembly) o `Pages_Host.cshtml` (Blazor Server):

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Para obtener más información sobre RCL y bibliotecas de componentes, vea lo siguiente:

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
