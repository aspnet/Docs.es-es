---
title: 'Diseños de ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Aprenda a crear componentes de diseño reutilizables para aplicaciones de :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/layouts
ms.openlocfilehash: e61c76b5d53ad7646961632d00b047ecd2d9e477
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055612"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="b9176-103">Diseños de ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="b9176-103">ASP.NET Core :::no-loc(Blazor)::: layouts</span></span>

<span data-ttu-id="b9176-104">Por [Rainer Stropek](https://www.timecockpit.com) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b9176-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b9176-105">Algunos elementos de la aplicación, como los menús, los mensajes de copyright y los logotipos de la empresa, normalmente forman parte del diseño general de la aplicación y se usan en todos sus componentes.</span><span class="sxs-lookup"><span data-stu-id="b9176-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="b9176-106">Copiar el código de estos elementos en todos los componentes de una aplicación no es una estrategia eficaz.</span><span class="sxs-lookup"><span data-stu-id="b9176-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="b9176-107">Cada vez que uno de los elementos necesita una actualización, todos los componentes deben actualizarse.</span><span class="sxs-lookup"><span data-stu-id="b9176-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="b9176-108">Esta duplicación es difícil de mantener y puede dar lugar a contenido incoherente con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="b9176-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="b9176-109">Los *diseños* solucionan este problema.</span><span class="sxs-lookup"><span data-stu-id="b9176-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="b9176-110">Técnicamente, un diseño es simplemente otro componente.</span><span class="sxs-lookup"><span data-stu-id="b9176-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="b9176-111">Un diseño se define en una plantilla de :::no-loc(Razor)::: o en código de C# y puede usar el [enlace de datos](xref:blazor/components/data-binding), la [inserción de dependencias](xref:blazor/fundamentals/dependency-injection) y otros escenarios de componente.</span><span class="sxs-lookup"><span data-stu-id="b9176-111">A layout is defined in a :::no-loc(Razor)::: template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="b9176-112">Para convertir un *componente* en un *diseño* , el componente debe:</span><span class="sxs-lookup"><span data-stu-id="b9176-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="b9176-113">Heredarse de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, que define una propiedad <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> para el contenido representado dentro del diseño.</span><span class="sxs-lookup"><span data-stu-id="b9176-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="b9176-114">Use la sintaxis `@Body` de :::no-loc(Razor)::: para especificar la ubicación en el marcado de diseño donde se representa el contenido.</span><span class="sxs-lookup"><span data-stu-id="b9176-114">Uses the :::no-loc(Razor)::: syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="b9176-115">En el ejemplo de código siguiente se muestra la plantilla de :::no-loc(Razor)::: de un componente de diseño, `MainLayout.razor`.</span><span class="sxs-lookup"><span data-stu-id="b9176-115">The following code sample shows the :::no-loc(Razor)::: template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="b9176-116">El diseño hereda <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> y establece `@Body` entre la barra de navegación y el pie de página:</span><span class="sxs-lookup"><span data-stu-id="b9176-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a><span data-ttu-id="b9176-117">Componente de `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="b9176-117">`MainLayout` component</span></span>

<span data-ttu-id="b9176-118">En una aplicación basada en una de las plantillas de proyecto de :::no-loc(Blazor):::, el componente `MainLayout` (`MainLayout.razor`) se encuentra en la carpeta `Shared` de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b9176-118">In an app based on one of the :::no-loc(Blazor)::: project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

[!code-razor[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a><span data-ttu-id="b9176-119">Diseño predeterminado</span><span class="sxs-lookup"><span data-stu-id="b9176-119">Default layout</span></span>

<span data-ttu-id="b9176-120">Especifique el diseño predeterminado de la aplicación en el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> en el archivo `App.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9176-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="b9176-121">El siguiente componente <xref:Microsoft.AspNetCore.Components.Routing.Router>, proporcionado por las plantillas predeterminadas de :::no-loc(Blazor):::, determina el diseño predeterminado para el componente `MainLayout`:</span><span class="sxs-lookup"><span data-stu-id="b9176-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default :::no-loc(Blazor)::: templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="b9176-122">Para proporcionar un diseño predeterminado para el contenido <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, especifique un elemento <xref:Microsoft.AspNetCore.Components.LayoutView> para el contenido <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>:</span><span class="sxs-lookup"><span data-stu-id="b9176-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="b9176-123">Para obtener más información sobre el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>, consulte el artículo <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b9176-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="b9176-124">Especificar el diseño como un diseño predeterminado en el enrutador es una práctica útil porque, después, se puede invalidar a nivel de componente o de carpeta.</span><span class="sxs-lookup"><span data-stu-id="b9176-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="b9176-125">Se recomienda usar el enrutador para establecer el diseño predeterminado de la aplicación, ya que es la técnica más general.</span><span class="sxs-lookup"><span data-stu-id="b9176-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="b9176-126">Especificación de un diseño en un componente</span><span class="sxs-lookup"><span data-stu-id="b9176-126">Specify a layout in a component</span></span>

<span data-ttu-id="b9176-127">Use la directiva `@layout` de :::no-loc(Razor)::: para aplicar un diseño a un componente.</span><span class="sxs-lookup"><span data-stu-id="b9176-127">Use the :::no-loc(Razor)::: directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="b9176-128">El compilador convierte `@layout` en un atributo <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, que se aplica a la clase de componentes.</span><span class="sxs-lookup"><span data-stu-id="b9176-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="b9176-129">El contenido del siguiente componente `MasterList` se inserta en `MasterLayout` en la posición `@Body`:</span><span class="sxs-lookup"><span data-stu-id="b9176-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="b9176-130">Al especificar el diseño directamente en un componente, se invalida el *diseño predeterminado* establecido en el enrutador o en una directiva `@layout` importada desde `_Imports.razor`.</span><span class="sxs-lookup"><span data-stu-id="b9176-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="b9176-131">Selección de diseño centralizada</span><span class="sxs-lookup"><span data-stu-id="b9176-131">Centralized layout selection</span></span>

<span data-ttu-id="b9176-132">Cada una de las carpetas de una aplicación puede contener opcionalmente un archivo de plantilla denominado `_Imports.razor`.</span><span class="sxs-lookup"><span data-stu-id="b9176-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="b9176-133">El compilador incluye las directivas especificadas en el archivo de importaciones de todas las plantillas de :::no-loc(Razor)::: de la misma carpeta y, de forma recurrente, de todas sus subcarpetas.</span><span class="sxs-lookup"><span data-stu-id="b9176-133">The compiler includes the directives specified in the imports file in all of the :::no-loc(Razor)::: templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="b9176-134">Por lo tanto, un archivo `_Imports.razor` que contiene `@layout MyCoolLayout` garantiza que todos los componentes de una carpeta usen `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="b9176-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="b9176-135">No es necesario agregar continuamente `@layout MyCoolLayout` a todos los archivos `.razor` dentro de la carpeta y las subcarpetas.</span><span class="sxs-lookup"><span data-stu-id="b9176-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="b9176-136">Las directivas `@using` también se aplican a los componentes de la misma manera.</span><span class="sxs-lookup"><span data-stu-id="b9176-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="b9176-137">El archivo `_Imports.razor` siguiente importa estos elementos:</span><span class="sxs-lookup"><span data-stu-id="b9176-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="b9176-138">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="b9176-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="b9176-139">Todos los componentes de :::no-loc(Razor)::: de la misma carpeta y cualquier subcarpeta.</span><span class="sxs-lookup"><span data-stu-id="b9176-139">All :::no-loc(Razor)::: components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="b9176-140">El espacio de nombres `:::no-loc(Blazor):::App1.Data` .</span><span class="sxs-lookup"><span data-stu-id="b9176-140">The `:::no-loc(Blazor):::App1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="b9176-141">El archivo `_Imports.razor` es similar al archivo [_ViewImports.cshtml de las vistas y las páginas de :::no-loc(Razor):::](xref:mvc/views/layout#importing-shared-directives), aunque se aplica específicamente a los archivos de componentes de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="b9176-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for :::no-loc(Razor)::: views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to :::no-loc(Razor)::: component files.</span></span>

<span data-ttu-id="b9176-142">Al especificar un diseño en `_Imports.razor`, se invalida un diseño especificado como *diseño predeterminado* del enrutador.</span><span class="sxs-lookup"><span data-stu-id="b9176-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="b9176-143">**No** agregue una directiva :::no-loc(Razor)::: `@layout` al archivo raíz `_Imports.razor`, que da como resultado un bucle infinito de diseños en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9176-143">Do **not** add a :::no-loc(Razor)::: `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="b9176-144">Para controlar el diseño predeterminado de la aplicación, especifique el diseño en el componente `Router`.</span><span class="sxs-lookup"><span data-stu-id="b9176-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="b9176-145">Para obtener más información, vea la sección [Diseño predeterminado](#default-layout).</span><span class="sxs-lookup"><span data-stu-id="b9176-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="b9176-146">Diseños anidados</span><span class="sxs-lookup"><span data-stu-id="b9176-146">Nested layouts</span></span>

<span data-ttu-id="b9176-147">Las aplicaciones pueden estar formadas por diseños anidados.</span><span class="sxs-lookup"><span data-stu-id="b9176-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="b9176-148">Un componente puede hacer referencia a un diseño que, a su vez, hace referencia a otro diseño.</span><span class="sxs-lookup"><span data-stu-id="b9176-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="b9176-149">Por ejemplo, los diseños anidados se usan para crear una estructura de menú de varios niveles.</span><span class="sxs-lookup"><span data-stu-id="b9176-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="b9176-150">En el ejemplo siguiente se muestra cómo usar los diseños anidados.</span><span class="sxs-lookup"><span data-stu-id="b9176-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="b9176-151">El archivo `EpisodesComponent.razor` es el componente que se va a mostrar.</span><span class="sxs-lookup"><span data-stu-id="b9176-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="b9176-152">El componente hace referencia a `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="b9176-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="b9176-153">El archivo `MasterListLayout.razor` proporciona `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="b9176-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="b9176-154">Este diseño hace referencia a otro diseño, `MasterLayout`, en el que se representa.</span><span class="sxs-lookup"><span data-stu-id="b9176-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="b9176-155">`EpisodesComponent` se representa donde aparece `@Body`:</span><span class="sxs-lookup"><span data-stu-id="b9176-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="b9176-156">Por último, `MasterLayout` en `MasterLayout.razor` contiene los elementos de diseño de nivel superior, como el encabezado, el menú principal y el pie de página.</span><span class="sxs-lookup"><span data-stu-id="b9176-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="b9176-157">`MasterListLayout` con el componente `EpisodesComponent` se representa donde aparece `@Body`:</span><span class="sxs-lookup"><span data-stu-id="b9176-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="b9176-158">Uso compartido de un diseño de :::no-loc(Razor)::: Pages con componentes integrados</span><span class="sxs-lookup"><span data-stu-id="b9176-158">Share a :::no-loc(Razor)::: Pages layout with integrated components</span></span>

<span data-ttu-id="b9176-159">Si los componentes enrutables se integran en una aplicación de :::no-loc(Razor)::: Pages, el diseño compartido de la aplicación se puede usar con los componentes.</span><span class="sxs-lookup"><span data-stu-id="b9176-159">When routable components are integrated into a :::no-loc(Razor)::: Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="b9176-160">Para obtener más información, vea <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="b9176-160">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9176-161">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b9176-161">Additional resources</span></span>

* <xref:mvc/views/layout>
