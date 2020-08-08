---
title: Diseño en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo usar diseños comunes, compartir directivas y ejecutar código común antes de representar vistas en una aplicación ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 4d5032f02db28341d7781dd57d58d776636fd16d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020449"
---
# <a name="layout-in-aspnet-core"></a>Diseño en ASP.NET Core

Por [Steve Smith](https://ardalis.com/) y [Dave Brock](https://twitter.com/daveabrock)

Las páginas y las vistas a menudo comparten elementos visuales y elementos mediante programación. En este artículo se explica cómo:

* Usar diseños comunes.
* Compartir directivas.
* Ejecutar código común antes de representar páginas o vistas.

En este documento se describen los diseños de los dos enfoques diferentes para ASP.NET Core MVC: Razor páginas y controladores con vistas. Para este tema, las diferencias son mínimas:

* RazorLas páginas se encuentran en la carpeta *pages* .
* Los controladores con vistas usan una carpeta *Vistas* para las vistas.

## <a name="what-is-a-layout"></a>Qué es un diseño

La mayoría de las aplicaciones web tienen un diseño común que ofrece al usuario una experiencia coherente mientras navegan por sus páginas. El diseño suele incluir elementos comunes en la interfaz de usuario, como el encabezado, los elementos de navegación o de menú y el pie de página de la aplicación.

![Ejemplo de diseño de página](layout/_static/page-layout.png)

Las estructuras HTML comunes, como scripts y hojas de estilo, también se usan con frecuencia en muchas páginas dentro de una aplicación. Todos estos elementos compartidos se pueden definir en un archivo de *diseño* , al que se puede hacer referencia a través de cualquier vista usada dentro de la aplicación. Los diseños reducen el código duplicado en las vistas.

Por convención, el diseño predeterminado para una aplicación ASP.NET Core se denomina *_Layout.cshtml*. Los archivos de diseño para los nuevos proyectos de ASP.NET Core creados con las plantillas son:

* RazorPáginas: *pages/Shared/_Layout. cshtml*

  ![Carpeta Pages del Explorador de soluciones](layout/_static/rp-web-project-views.png)

* Controlador con vistas: *Views/Shared/_Layout.cshtml*

  ![Carpeta Views del Explorador de soluciones](layout/_static/mvc-web-project-views.png)

Este diseño define una plantilla de nivel superior para las vistas en la aplicación. Las aplicaciones no requieren un diseño. Las aplicaciones pueden definir más de un diseño, con otras vistas que especifiquen otros diseños.

Este código muestra el archivo de diseño para un proyecto creado mediante plantilla con un controlador y vistas:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Especificar un diseño

Razorlas vistas tienen una `Layout` propiedad. Las vistas individuales especifican un diseño al configurar esta propiedad:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

El diseño especificado puede usar una ruta de acceso completa (por ejemplo, */Pages/Shared/_Layout.cshtml* o */Views/Shared/_Layout.cshtml*) o un nombre parcial (ejemplo: `_Layout`). Cuando se proporciona un nombre parcial, el Razor motor de vistas busca el archivo de diseño mediante su proceso de detección estándar. Primero se busca la carpeta donde existe el método de controlador (o controlador), seguida de la carpeta *Shared*. Este proceso de detección es idéntico al que se usa para detectar [vistas parciales](xref:mvc/views/partial#partial-view-discovery).

De forma predeterminada, todos los diseños deben llamar a `RenderBody`. Cada vez que se realiza la llamada a `RenderBody`, se representa el contenido de la vista.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>Secciones

Opcionalmente, un diseño puede hacer referencia a una o varias *secciones* mediante una llamada a `RenderSection`. Las secciones permiten organizar dónde se deben colocar determinados elementos de la página. Cada llamada a `RenderSection` puede especificar si esa sección es obligatoria u opcional:

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

Si no se encuentra una sección obligatoria, se produce una excepción. Las vistas individuales especifican el contenido que se va a representar dentro de una sección mediante la `@section` Razor Sintaxis. Si una página o una vista define una sección, se debe representar (o se producirá un error).

Una `@section` definición de ejemplo en la Razor vista páginas:

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

En el código anterior, *scripts/main.js* se agrega a la sección `scripts` en una página o vista. Es posible que otras páginas o vistas de la misma aplicación no necesiten este script y no definan una sección de script.

El marcado siguiente usa el [asistente de etiquetas parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) para representar *_ValidationScriptsPartial.cshtml*:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

El marcado anterior se generó mediante [scaffolding Identity ](xref:security/authentication/scaffold-identity).

Las secciones definidas en una vista o una vista solo están disponibles en su página de diseño inmediato. No se puede hacer referencia a ellas desde líneas de código parcialmente ejecutadas, componentes de vista u otros elementos del sistema de vistas.

### <a name="ignoring-sections"></a>Omitir secciones

De forma predeterminada, el cuerpo y todas las secciones de una página de contenido deben representarse mediante la página de diseño. El Razor motor de vistas exige esto mediante el seguimiento de si se han representado el cuerpo y cada sección.

Para indicar al motor de vistas que pase por alto el cuerpo o las secciones, llame a los métodos `IgnoreBody` y `IgnoreSection`.

El cuerpo y todas las secciones de una Razor Página deben representarse o pasarse por alto.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importar directivas compartidas

Las vistas y las páginas pueden utilizar Razor directivas para importar espacios de nombres y usar la [inserción de dependencias](dependency-injection.md). Se pueden especificar varias directivas compartidas por muchas vistas en un archivo *_ViewImports.cshtml* común. El archivo `_ViewImports` es compatible con estas directivas:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

El archivo no es compatible con otras Razor características, como las funciones y las definiciones de la sección.

Archivo `_ViewImports.cshtml` de ejemplo:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

El archivo *_ViewImports.cshtml* para una aplicación ASP.NET Core MVC normalmente se coloca en la carpeta *Pages* (o *Views*). Un archivo *_ViewImports.cshtml* puede colocarse dentro de cualquier carpeta, en cuyo caso solo se aplicará a páginas o vistas dentro de esa carpeta y sus subcarpetas. Los archivos `_ViewImports` se procesan a partir del nivel de raíz y, después, para cada carpeta que llevó a la ubicación de la propia página o vista. La configuración `_ViewImports` especificada en el nivel de raíz se puede reemplazar en el nivel de carpeta.

Por ejemplo, supongamos que:

* El archivo *_ViewImports.cshtml* del nivel de raíz incluye `@model MyModel1` y `@addTagHelper *, MyTagHelper1`.
* Un archivo *_ViewImports.cshtml* de subcarpeta incluye `@model MyModel2` y `@addTagHelper *, MyTagHelper2`.

Las páginas y las vistas de la subcarpeta tendrán acceso a los asistentes de etiquetas y al modelo `MyModel2`.

Si hay varios *_ViewImports.cshtml* en la jerarquía de archivos, el comportamiento combinado de las directivas es:

* `@addTagHelper`, `@removeTagHelper`: todos se ejecutan en orden
* `@tagHelperPrefix`: el más cercano a la vista invalida los demás
* `@model`: el más cercano a la vista invalida los demás
* `@inherits`: el más cercano a la vista invalida los demás
* `@using`: todos se incluyen y se omiten los duplicados
* `@inject`: para cada propiedad, la más cercana a la vista invalida cualquier otra con el mismo nombre de propiedad

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Ejecutar código antes de cada vista

El código que debe ejecutarse antes de cada vista o página se debería colocar en el archivo *_ViewStart.cshtml*. Por convención, el archivo *_ViewStart.cshtml* se encuentra en la carpeta *Pages* (o *Views*). Las instrucciones que aparecen en *_ViewStart.cshtml* se ejecutan antes de cada vista completa (no los diseños ni las vistas parciales). Al igual que [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* tiene una estructura jerárquica. Si se define un archivo *_ViewStart.cshtml* en la carpeta de vista o de página, se ejecutará después del que esté definido en la raíz de la carpeta *Pages* (o *Views*) (si existe).

Un archivo *_ViewStart.cshtml* de ejemplo:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

El archivo anterior especifica que todas las vistas usarán el diseño *_Layout.cshtml*.

*_ViewStart.cshtml* y *_ViewImports.cshtml* normalmente **no** se colocan en la carpeta */Pages/Shared* (o * /Views/Shared*). Las versiones de nivel de aplicación de estos archivos deben colocarse directamente en la carpeta */Pages* (o */Views*).
