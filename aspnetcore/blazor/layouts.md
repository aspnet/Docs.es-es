---
title: Diseños de ASP.NET Core Blazor
author: guardrex
description: Aprenda a crear componentes de diseño reutilizables para aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: e61c76b5d53ad7646961632d00b047ecd2d9e477
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055612"
---
# <a name="aspnet-core-no-locblazor-layouts"></a>Diseños de ASP.NET Core Blazor

Por [Rainer Stropek](https://www.timecockpit.com) y [Luke Latham](https://github.com/guardrex)

Algunos elementos de la aplicación, como los menús, los mensajes de copyright y los logotipos de la empresa, normalmente forman parte del diseño general de la aplicación y se usan en todos sus componentes. Copiar el código de estos elementos en todos los componentes de una aplicación no es una estrategia eficaz. Cada vez que uno de los elementos necesita una actualización, todos los componentes deben actualizarse. Esta duplicación es difícil de mantener y puede dar lugar a contenido incoherente con el tiempo. Los *diseños* solucionan este problema.

Técnicamente, un diseño es simplemente otro componente. Un diseño se define en una plantilla de Razor o en código de C# y puede usar el [enlace de datos](xref:blazor/components/data-binding), la [inserción de dependencias](xref:blazor/fundamentals/dependency-injection) y otros escenarios de componente.

Para convertir un *componente* en un *diseño* , el componente debe:

* Heredarse de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, que define una propiedad <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> para el contenido representado dentro del diseño.
* Use la sintaxis `@Body` de Razor para especificar la ubicación en el marcado de diseño donde se representa el contenido.

En el ejemplo de código siguiente se muestra la plantilla de Razor de un componente de diseño, `MainLayout.razor`. El diseño hereda <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> y establece `@Body` entre la barra de navegación y el pie de página:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a>Componente de `MainLayout`

En una aplicación basada en una de las plantillas de proyecto de Blazor, el componente `MainLayout` (`MainLayout.razor`) se encuentra en la carpeta `Shared` de la aplicación:

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a>Diseño predeterminado

Especifique el diseño predeterminado de la aplicación en el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> en el archivo `App.razor` de la aplicación. El siguiente componente <xref:Microsoft.AspNetCore.Components.Routing.Router>, proporcionado por las plantillas predeterminadas de Blazor, determina el diseño predeterminado para el componente `MainLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Para proporcionar un diseño predeterminado para el contenido <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, especifique un elemento <xref:Microsoft.AspNetCore.Components.LayoutView> para el contenido <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Para obtener más información sobre el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>, consulte el artículo <xref:blazor/fundamentals/routing>.

Especificar el diseño como un diseño predeterminado en el enrutador es una práctica útil porque, después, se puede invalidar a nivel de componente o de carpeta. Se recomienda usar el enrutador para establecer el diseño predeterminado de la aplicación, ya que es la técnica más general.

## <a name="specify-a-layout-in-a-component"></a>Especificación de un diseño en un componente

Use la directiva `@layout` de Razor para aplicar un diseño a un componente. El compilador convierte `@layout` en un atributo <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, que se aplica a la clase de componentes.

El contenido del siguiente componente `MasterList` se inserta en `MasterLayout` en la posición `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Al especificar el diseño directamente en un componente, se invalida el *diseño predeterminado* establecido en el enrutador o en una directiva `@layout` importada desde `_Imports.razor`.

## <a name="centralized-layout-selection"></a>Selección de diseño centralizada

Cada una de las carpetas de una aplicación puede contener opcionalmente un archivo de plantilla denominado `_Imports.razor`. El compilador incluye las directivas especificadas en el archivo de importaciones de todas las plantillas de Razor de la misma carpeta y, de forma recurrente, de todas sus subcarpetas. Por lo tanto, un archivo `_Imports.razor` que contiene `@layout MyCoolLayout` garantiza que todos los componentes de una carpeta usen `MyCoolLayout`. No es necesario agregar continuamente `@layout MyCoolLayout` a todos los archivos `.razor` dentro de la carpeta y las subcarpetas. Las directivas `@using` también se aplican a los componentes de la misma manera.

El archivo `_Imports.razor` siguiente importa estos elementos:

* `MyCoolLayout`.
* Todos los componentes de Razor de la misma carpeta y cualquier subcarpeta.
* El espacio de nombres `BlazorApp1.Data` .
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

El archivo `_Imports.razor` es similar al archivo [_ViewImports.cshtml de las vistas y las páginas de Razor](xref:mvc/views/layout#importing-shared-directives), aunque se aplica específicamente a los archivos de componentes de Razor.

Al especificar un diseño en `_Imports.razor`, se invalida un diseño especificado como *diseño predeterminado* del enrutador.

> [!WARNING]
> **No** agregue una directiva Razor `@layout` al archivo raíz `_Imports.razor`, que da como resultado un bucle infinito de diseños en la aplicación. Para controlar el diseño predeterminado de la aplicación, especifique el diseño en el componente `Router`. Para obtener más información, vea la sección [Diseño predeterminado](#default-layout).

## <a name="nested-layouts"></a>Diseños anidados

Las aplicaciones pueden estar formadas por diseños anidados. Un componente puede hacer referencia a un diseño que, a su vez, hace referencia a otro diseño. Por ejemplo, los diseños anidados se usan para crear una estructura de menú de varios niveles.

En el ejemplo siguiente se muestra cómo usar los diseños anidados. El archivo `EpisodesComponent.razor` es el componente que se va a mostrar. El componente hace referencia a `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

El archivo `MasterListLayout.razor` proporciona `MasterListLayout`. Este diseño hace referencia a otro diseño, `MasterLayout`, en el que se representa. `EpisodesComponent` se representa donde aparece `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Por último, `MasterLayout` en `MasterLayout.razor` contiene los elementos de diseño de nivel superior, como el encabezado, el menú principal y el pie de página. `MasterListLayout` con el componente `EpisodesComponent` se representa donde aparece `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a>Uso compartido de un diseño de Razor Pages con componentes integrados

Si los componentes enrutables se integran en una aplicación de Razor Pages, el diseño compartido de la aplicación se puede usar con los componentes. Para obtener más información, vea <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/layout>
