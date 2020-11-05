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
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056327"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>Aislamiento de CSS de Blazor de ASP.NET Core

De [Dave Brock](https://twitter.com/daveabrock)

El aislamiento de CSS simplifica la superficie de CSS de una aplicación al evitar dependencias en estilos globales y ayuda a evitar conflictos de estilo entre componentes y bibliotecas.

## <a name="enable-css-isolation"></a>Habilitación del aislamiento de CSS 

Para definir estilos específicos de un componente, cree un archivo `razor.css` cuyo nombre coincida con el del archivo `.razor` del componente. Este archivo `razor.css` es un *archivo CSS con ámbito*. 

En un componente `MyComponent` que tenga un archivo `MyComponent.razor`, cree un archivo junto al componente denominado `MyComponent.razor.css`. El valor `MyComponent` del nombre de archivo `razor.css` **no** distingue mayúsculas de minúsculas.

Por ejemplo, para agregar aislamiento de CSS al componente `Counter` de la plantilla de proyecto de Blazor predeterminada, agregue un nuevo archivo denominado `Counter.razor.css` junto con el archivo `Counter.razor` y luego agregue el siguiente CSS:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

Los estilos definidos en `Counter.razor.css` solo se aplican a la salida representada del componente `Counter`. Cualquier declaración CSS `h1` definida en otra ubicación de la aplicación no entra en conflicto con los estilos de `Counter`.

> [!NOTE]
> Con el fin de garantizar el aislamiento de estilo en el momento de la unión, no se admiten bloques de Razor `@import` con archivos CSS con ámbito.

## <a name="css-isolation-bundling"></a>Unión del aislamiento de CSS

El aislamiento de CSS se produce en tiempo de compilación. Durante este proceso, Blazor reescribe los selectores de CSS para que coincidan con el marcado representado por el componente. Estos estilos de CSS reescritos se unen y se generan como un recurso estático en `{PROJECT NAME}.styles.css`, donde el marcador de posición `{PROJECT NAME}` es el nombre del paquete o del producto al que se hace referencia.

De forma predeterminada, se hace referencia a estos archivos estáticos desde la ruta de acceso raíz de la aplicación. En la aplicación, haga referencia al archivo unido mediante la referencia dentro de la etiqueta `<head>` del HTML generado:

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

En el archivo unido, cada componente está asociado a un identificador de ámbito. En cada componente con estilo, se anexa un atributo HTML con el formato `b-<10-character-string>`. El identificador es único y diferente para cada aplicación. En el componente `Counter` representado, Blazor anexa un identificador de ámbito al elemento `h1`:

```html
<h1 b-3xxtam6d07>
```

El archivo `MyProjectName.styles.css` usa el identificador de ámbito para agrupar una declaración de estilo con su componente. En el ejemplo siguiente se proporciona el estilo del elemento `<h1>` anterior:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

En tiempo de compilación, se crea un conjunto del proyecto con la convención `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, donde el marcador de posición `{STATIC WEB ASSETS BASE PATH}` es la ruta de acceso base a los recursos web estáticos.

Si se usan otros proyectos, como paquetes NuGet o [bibliotecas de clases de Razor](xref:blazor/components/class-libraries), el archivo unido:

* Hace referencia a los estilos mediante importaciones de CSS.
* No se publica como recurso web estático de la aplicación que consume los estilos.

## <a name="child-component-support"></a>Compatibilidad de componente secundario

De forma predeterminada, el aislamiento de CSS solo se aplica al componente que se asocia con el formato `{COMPONENT NAME}.razor.css`, donde el marcador de posición `{COMPONENT NAME}` suele ser el nombre del componente. Para aplicar cambios a un componente secundario, use el combinador `::deep` con los elementos descendientes del archivo `razor.css` del componente primario. El combinador `::deep` selecciona los elementos que son *descendientes* del identificador de ámbito generado de un elemento. 

En el ejemplo siguiente se muestra un componente primario denominado `Parent` con un componente secundario denominado `Child`.

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

Actualice la declaración `h1` de `Parent.razor.css` con el combinador `::deep` para indicar que la declaración de estilo de `h1` debe aplicarse al componente primario y a sus elementos secundarios:

```css
::deep h1 { 
    color: red;
}
```

El estilo de `h1` ahora se aplica a los componentes `Parent` y `Child` sin necesidad de crear un archivo CSS con ámbito independiente para el componente secundario.

> [!NOTE]
> El combinador `::deep` solo funciona con elementos descendientes. La siguiente estructura HTML aplica los estilos de `h1` a los componentes según lo esperado:
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> En este escenario, ASP.NET Core aplica el identificador de ámbito del componente primario al elemento `div`, por lo que se sabe que el explorador hereda los estilos del componente primario.
>
> Pero, si se excluye el elemento `div`, se quita la relación de descendiente y el estilo **no** se aplica al componente secundario:
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>Compatibilidad del preprocesador de CSS

Los preprocesadores de CSS son útiles para mejorar el desarrollo de CSS mediante el uso de características como variables, anidamiento, módulos, mixins y herencia. Aunque el aislamiento de CSS no admite de forma nativa preprocesadores de CSS como Sass o Less, la integración de preprocesadores de CSS se realiza sin problemas siempre que se produzca la compilación del preprocesador antes de que Blazor reescriba los selectores de CSS durante el proceso de compilación. Con Visual Studio, por ejemplo, configure la compilación del preprocesador existente como una tarea **Antes de la compilación** en el Explorador del Ejecutor de tareas de Visual Studio.

Muchos paquetes NuGet de terceros, como [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), pueden compilar archivos SASS/SCSS al principio del proceso de compilación antes de que se produzca el aislamiento de CSS y no requieren ninguna configuración adicional.

## <a name="css-isolation-configuration"></a>Configuración del aislamiento de CSS

El aislamiento de CSS está diseñado para poder trabajar de inmediato, pero proporciona configuración para algunos escenarios avanzados, por ejemplo cuando hay dependencias en herramientas o flujos de trabajo existentes.

### <a name="customize-scope-identifier-format"></a>Personalización del formato del identificador de ámbito

De forma predeterminada, los identificadores de ámbito usan el formato `b-<10-character-string>`. Para personalizar el formato de un identificador de ámbito, actualice el archivo del proyecto a un patrón deseado:

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

En el ejemplo anterior, el CSS generado para `MyComponent.Razor.css` cambia su identificador de ámbito de `b-<10-character-string>` a `my-custom-scope-identifier`.

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
