---
title: Creación y uso de componentes de Razor de ASP.NET Core
author: guardrex
description: Aprenda a crear y usar componentes de Razor, lo que incluye cómo enlazar a datos, controlar eventos y administrar los ciclos de vida de los componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 12a1f528bdff0230bbf17075284d27de654a423e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252427"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="b7c45-103">Creación y uso de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7c45-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="b7c45-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie) y [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="b7c45-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="b7c45-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7c45-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b7c45-106">Las aplicaciones Blazor se crean usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="b7c45-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="b7c45-107">Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="b7c45-108">Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="b7c45-109">Además, son flexibles y ligeros,</span><span class="sxs-lookup"><span data-stu-id="b7c45-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="b7c45-110">y se pueden anidar, reutilizar y compartir entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="b7c45-111">Clases de componentes</span><span class="sxs-lookup"><span data-stu-id="b7c45-111">Component classes</span></span>

<span data-ttu-id="b7c45-112">Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) (`.razor`) mediante una combinación de C# y marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="b7c45-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="b7c45-113">Un componente de Blazor se conoce formalmente como *componente de Razor* .</span><span class="sxs-lookup"><span data-stu-id="b7c45-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="b7c45-114">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="b7c45-114">Razor syntax</span></span>

<span data-ttu-id="b7c45-115">Los componentes Razor de las aplicaciones Blazor usan de manera generalizada la sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="b7c45-116">Si no está familiarizado con el lenguaje de marcado de Razor, se recomienda leer la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b7c45-116">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="b7c45-117">Al acceder al contenido de la sintaxis Razor, preste especial atención a las siguientes secciones:</span><span class="sxs-lookup"><span data-stu-id="b7c45-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="b7c45-118">[Directivas](xref:mvc/views/razor#directives): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analiza o funciona el marcado de los componentes.</span><span class="sxs-lookup"><span data-stu-id="b7c45-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="b7c45-119">[Atributos de directiva](xref:mvc/views/razor#directive-attributes): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analizan o funcionan los elementos de los componentes.</span><span class="sxs-lookup"><span data-stu-id="b7c45-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="b7c45-120">Nombres</span><span class="sxs-lookup"><span data-stu-id="b7c45-120">Names</span></span>

<span data-ttu-id="b7c45-121">El nombre de un componente debe empezar por mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="b7c45-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="b7c45-122">Por ejemplo, `MyCoolComponent.razor` no es válido, pero `myCoolComponent.razor` sí.</span><span class="sxs-lookup"><span data-stu-id="b7c45-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="b7c45-123">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="b7c45-123">Routing</span></span>

<span data-ttu-id="b7c45-124">El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7c45-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="b7c45-125">Cuando se compila un archivo de Razor con una directiva [`@page`][9], la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> que especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="b7c45-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="b7c45-126">En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="b7c45-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="b7c45-127">Para obtener más información, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="b7c45-128">marcado</span><span class="sxs-lookup"><span data-stu-id="b7c45-128">Markup</span></span>

<span data-ttu-id="b7c45-129">La interfaz de usuario de un componente se define mediante HTML.</span><span class="sxs-lookup"><span data-stu-id="b7c45-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="b7c45-130">La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="b7c45-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="b7c45-131">Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="b7c45-132">El nombre de la clase generada coincide con el nombre del archivo.</span><span class="sxs-lookup"><span data-stu-id="b7c45-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="b7c45-133">Los miembros de la clase de componente se definen en un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="b7c45-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="b7c45-134">En el bloque [`@code`][1], el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="b7c45-135">Se permite emplear más de un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="b7c45-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="b7c45-136">Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="b7c45-137">Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="b7c45-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="b7c45-138">En el siguiente ejemplo se evalúa y representa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="b7c45-139">`headingFontStyle` del valor de la propiedad CSS de `font-style`</span><span class="sxs-lookup"><span data-stu-id="b7c45-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="b7c45-140">`headingText` del contenido del elemento `<h1>`</span><span class="sxs-lookup"><span data-stu-id="b7c45-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="b7c45-141">Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="b7c45-142">Blazor compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="b7c45-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="b7c45-143">En <xref:blazor/components/rendering> se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="b7c45-143">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="b7c45-144">Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7c45-144">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="b7c45-145">Los componentes que generan páginas web suelen residir en la carpeta `Pages`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-145">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="b7c45-146">Los componentes que no son de página se colocan con frecuencia en la carpeta `Shared` o en una carpeta personalizada agregada al proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7c45-146">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="b7c45-147">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="b7c45-147">Namespaces</span></span>

<span data-ttu-id="b7c45-148">Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7c45-148">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="b7c45-149">Así, si el espacio de nombres raíz de la aplicación es `BlazorSample` y el componente `Counter` reside en la carpeta `Pages`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-149">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="b7c45-150">El espacio de nombres del componente `Counter` es `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-150">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="b7c45-151">El nombre de tipo completo del componente es `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-151">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="b7c45-152">En el caso de las carpetas personalizadas que contienen componentes, agregue una directiva [`@using`][2] al componente principal o al archivo `_Imports.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7c45-152">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="b7c45-153">En el ejemplo siguiente se ponen a disposición los componentes de la carpeta `Components`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-153">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="b7c45-154">También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="b7c45-154">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="b7c45-155">El espacio de nombres de un componente creado con Razor se basa en (por orden de prioridad):</span><span class="sxs-lookup"><span data-stu-id="b7c45-155">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="b7c45-156">Designación [`@namespace`][8] del marcado del archivo de Razor (`.razor`) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-156">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="b7c45-157">Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-157">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="b7c45-158">Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto (`.csproj`) y la ruta de acceso de la raíz del proyecto al componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-158">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="b7c45-159">Por ejemplo, el marco de trabajo resuelve `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) en el espacio de nombres `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-159">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="b7c45-160">Los componentes de C# siguen las reglas de los enlaces de nombres.</span><span class="sxs-lookup"><span data-stu-id="b7c45-160">Components follow C# name binding rules.</span></span> <span data-ttu-id="b7c45-161">En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:</span><span class="sxs-lookup"><span data-stu-id="b7c45-161">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="b7c45-162">Ejecute `Pages` en la misma carpeta.</span><span class="sxs-lookup"><span data-stu-id="b7c45-162">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="b7c45-163">Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-163">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="b7c45-164">La calificación `global::` no se puede usar.</span><span class="sxs-lookup"><span data-stu-id="b7c45-164">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="b7c45-165">No se pueden importar componentes con instrucciones [`using`](/dotnet/csharp/language-reference/keywords/using-statement) con alias (por ejemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-165">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="b7c45-166">No se pueden usar nombres parciales.</span><span class="sxs-lookup"><span data-stu-id="b7c45-166">Partially qualified names aren't supported.</span></span> <span data-ttu-id="b7c45-167">Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia al componente `NavMenu` (`NavMenu.razor`) con `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-167">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="b7c45-168">Compatibilidad parcial de clases</span><span class="sxs-lookup"><span data-stu-id="b7c45-168">Partial class support</span></span>

<span data-ttu-id="b7c45-169">Los componentes de Razor se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="b7c45-169">Razor components are generated as partial classes.</span></span> <span data-ttu-id="b7c45-170">Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="b7c45-170">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="b7c45-171">Se define código de C# en un bloque [`@code`][1] con marcado HTML y código de Razor en un solo archivo.</span><span class="sxs-lookup"><span data-stu-id="b7c45-171">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="b7c45-172">Las plantillas de Blazor definen sus componentes de Razor con este método.</span><span class="sxs-lookup"><span data-stu-id="b7c45-172">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="b7c45-173">El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.</span><span class="sxs-lookup"><span data-stu-id="b7c45-173">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="b7c45-174">En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque [`@code`][1] en una aplicación generada a partir de una plantilla de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-174">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="b7c45-175">El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:</span><span class="sxs-lookup"><span data-stu-id="b7c45-175">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="b7c45-176">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-176">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="b7c45-177">El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:</span><span class="sxs-lookup"><span data-stu-id="b7c45-177">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="b7c45-178">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-178">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="b7c45-179">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-179">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="b7c45-180">Agregue los espacios de nombres que sean necesarios al archivo de clase parcial.</span><span class="sxs-lookup"><span data-stu-id="b7c45-180">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="b7c45-181">Los espacios de nombres típicos usados por los componentes de Razor incluyen:</span><span class="sxs-lookup"><span data-stu-id="b7c45-181">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="b7c45-182">Las directivas [`@using`][2] del archivo `_Imports.razor` solo se aplican a los archivos Razor (`.razor`), por lo que no se aplican a los archivos C# (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-182">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="b7c45-183">Especificación de una clase base</span><span class="sxs-lookup"><span data-stu-id="b7c45-183">Specify a base class</span></span>

<span data-ttu-id="b7c45-184">La directiva [`@inherits`][6] se puede usar para especificar la clase base de un componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-184">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="b7c45-185">En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-185">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="b7c45-186">La clase base debe derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-186">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="b7c45-187">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-187">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="b7c45-188">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-188">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="b7c45-189">Uso de componentes</span><span class="sxs-lookup"><span data-stu-id="b7c45-189">Use components</span></span>

<span data-ttu-id="b7c45-190">Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="b7c45-190">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="b7c45-191">El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-191">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="b7c45-192">El siguiente marcado en `Pages/Index.razor` representa una instancia de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-192">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="b7c45-193">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-193">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="b7c45-194">Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado.</span><span class="sxs-lookup"><span data-stu-id="b7c45-194">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="b7c45-195">Al agregar una directiva [`@using`][2] relativa al espacio de nombres del componente, se permite que el componente esté disponible, lo que resuelve la advertencia.</span><span class="sxs-lookup"><span data-stu-id="b7c45-195">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="b7c45-196">Parámetros</span><span class="sxs-lookup"><span data-stu-id="b7c45-196">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="b7c45-197">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="b7c45-197">Route parameters</span></span>

<span data-ttu-id="b7c45-198">Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="b7c45-198">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="b7c45-199">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="b7c45-199">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7c45-200">Se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="b7c45-200">Optional parameters are supported.</span></span> <span data-ttu-id="b7c45-201">En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-201">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b7c45-202">Si el segmento no está presente, el valor de `Text` se establece en `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-202">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b7c45-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b7c45-204">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-204">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="b7c45-205">No se admiten parámetros opcionales, por lo que en el ejemplo anterior se aplican dos directivas [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="b7c45-205">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="b7c45-206">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="b7c45-206">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b7c45-207">mientras que la segunda directiva [`@page`][9] recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-207">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="b7c45-208">Para obtener información sobre los parámetros de ruta comodín (`{*pageRoute}`), que capturan rutas de acceso en varios límites de carpeta, vea <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-208">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="b7c45-209">Parámetros del componente</span><span class="sxs-lookup"><span data-stu-id="b7c45-209">Component parameters</span></span>

<span data-ttu-id="b7c45-210">Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas simples o complejas en la clase del componente con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="b7c45-210">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="b7c45-211">Use atributos para especificar argumentos para un componente en el marcado.</span><span class="sxs-lookup"><span data-stu-id="b7c45-211">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="b7c45-212">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-212">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="b7c45-213">Se puede asignar un valor predeterminado a los parámetros de componente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-213">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="b7c45-214">En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-214">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="b7c45-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-215">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="b7c45-216">Por convención, un valor de atributo que se compone de código de C# se asigna a un parámetro usando el [`@` símbolo reservado de Razor](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="b7c45-216">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="b7c45-217">Propiedad o campo primario: `Title="@{FIELD OR PROPERTY}`, donde el marcador de posición `{FIELD OR PROPERTY}` es un campo o propiedad de C# del componente primario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-217">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="b7c45-218">Resultado de un método: `Title="@{METHOD}"`, donde el marcador de posición `{METHOD}` es un método de C# del componente primario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-218">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="b7c45-219">[Expresión implícita o explícita](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, donde el marcador de posición `{EXPRESSION}` es una expresión de C#.</span><span class="sxs-lookup"><span data-stu-id="b7c45-219">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="b7c45-220">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b7c45-220">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="b7c45-221">No cree componentes que escriban en sus propios *parámetros de componente*; en su lugar, use un campo privado.</span><span class="sxs-lookup"><span data-stu-id="b7c45-221">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="b7c45-222">Para obtener más información, vea la sección [Parámetros sobrescritos](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="b7c45-222">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="b7c45-223">Contenido secundario</span><span class="sxs-lookup"><span data-stu-id="b7c45-223">Child content</span></span>

<span data-ttu-id="b7c45-224">Los componentes pueden definir el contenido de otro componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-224">Components can set the content of another component.</span></span> <span data-ttu-id="b7c45-225">El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-225">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="b7c45-226">En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment>, que representa a su vez un segmento de interfaz de usuario que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="b7c45-226">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="b7c45-227">El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse.</span><span class="sxs-lookup"><span data-stu-id="b7c45-227">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="b7c45-228">El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.</span><span class="sxs-lookup"><span data-stu-id="b7c45-228">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="b7c45-229">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-229">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="b7c45-230">La propiedad que recibe el contenido de <xref:Microsoft.AspNetCore.Components.RenderFragment> debe denominarse `ChildContent` por convención.</span><span class="sxs-lookup"><span data-stu-id="b7c45-230">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="b7c45-231">El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-231">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="b7c45-232">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-232">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="b7c45-233">Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente secundario:</span><span class="sxs-lookup"><span data-stu-id="b7c45-233">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="b7c45-234">También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="b7c45-234">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="b7c45-235">Expansión de atributos y parámetros arbitrarios</span><span class="sxs-lookup"><span data-stu-id="b7c45-235">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="b7c45-236">Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-236">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="b7c45-237">Los atributos adicionales se pueden capturar en un diccionario y luego *expandirse* en un elemento cuando el componente se representa por medio de la directiva de Razor [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="b7c45-237">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="b7c45-238">Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="b7c45-238">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="b7c45-239">Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.</span><span class="sxs-lookup"><span data-stu-id="b7c45-239">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="b7c45-240">En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:</span><span class="sxs-lookup"><span data-stu-id="b7c45-240">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="b7c45-241">El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="b7c45-241">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="b7c45-242">Los elementos `<input>` representados con ambos métodos son idénticos:</span><span class="sxs-lookup"><span data-stu-id="b7c45-242">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="b7c45-243">Para aceptar atributos arbitrarios, defina un parámetro de componente usando el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) con la propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> establecida en `true`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-243">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="b7c45-244">La propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro.</span><span class="sxs-lookup"><span data-stu-id="b7c45-244">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="b7c45-245">Un componente solamente puede definir un parámetro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-245">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="b7c45-246">El tipo de propiedad que se usa con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> se debe poder asignar desde `Dictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="b7c45-246">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="b7c45-247">`IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-247">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="b7c45-248">La posición de [`@attributes`][3] relativa a la posición de los atributos de elemento es importante.</span><span class="sxs-lookup"><span data-stu-id="b7c45-248">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="b7c45-249">Cuando [`@attributes`][3] se expande en el elemento, los atributos se procesan de derecha a izquierda (del último al primero).</span><span class="sxs-lookup"><span data-stu-id="b7c45-249">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="b7c45-250">Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-250">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="b7c45-251">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-251">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b7c45-252">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-252">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b7c45-253">El atributo `extra` del componente `Child` se establece a la derecha de [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="b7c45-253">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="b7c45-254">El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):</span><span class="sxs-lookup"><span data-stu-id="b7c45-254">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="b7c45-255">En el ejemplo siguiente, el orden de `extra` y [`@attributes`][3] se invierte en el elemento `Child` del componente `<div>`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-255">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="b7c45-256">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-256">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b7c45-257">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-257">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b7c45-258">El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="b7c45-258">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="b7c45-259">Captura de referencias a componentes</span><span class="sxs-lookup"><span data-stu-id="b7c45-259">Capture references to components</span></span>

<span data-ttu-id="b7c45-260">Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-260">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="b7c45-261">Para capturar una referencia de componente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-261">To capture a component reference:</span></span>

* <span data-ttu-id="b7c45-262">Agregue un atributo [`@ref`][4] al componente secundario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-262">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="b7c45-263">Defina un campo con el mismo tipo que el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-263">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="b7c45-264">Cuando el componente se represente, el campo `loginDialog` se rellena con la instancia del componente secundario `CustomLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-264">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="b7c45-265">Tras ello, se pueden invocar métodos de .NET en la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-265">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7c45-266">La variable `loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-266">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="b7c45-267">Hasta que se represente el componente, no hay nada a lo que hacer referencia.</span><span class="sxs-lookup"><span data-stu-id="b7c45-267">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="b7c45-268">Para manipular las referencias de componente una vez finalizada la representación del componente, use los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b7c45-268">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="b7c45-269">Para usar una variable de referencia con un controlador de eventos, use una expresión lambda o asigne el delegado de controlador de eventos en los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b7c45-269">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="b7c45-270">Esto garantiza que la variable de referencia se asigna antes de que se asigne el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-270">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="b7c45-271">Para hacer referencias a componentes de un bucle, consulte el artículo sobre la [captura de referencias a varios componentes secundarios similares (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="b7c45-271">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="b7c45-272">Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b7c45-272">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="b7c45-273">Las referencias de componente no se pasan al código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b7c45-273">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="b7c45-274">Solo se usan en código .NET.</span><span class="sxs-lookup"><span data-stu-id="b7c45-274">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="b7c45-275">**No** use referencias de componentes para mutar el estado de los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="b7c45-275">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="b7c45-276">En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="b7c45-276">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="b7c45-277">El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.</span><span class="sxs-lookup"><span data-stu-id="b7c45-277">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="b7c45-278">Contexto de sincronización</span><span class="sxs-lookup"><span data-stu-id="b7c45-278">Synchronization context</span></span>

<span data-ttu-id="b7c45-279">Blazor usa un contexto de sincronización (<xref:System.Threading.SynchronizationContext>) para aplicar un único subproceso lógico de ejecución.</span><span class="sxs-lookup"><span data-stu-id="b7c45-279">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="b7c45-280">Los [métodos de ciclo de vida](xref:blazor/components/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en el contexto de sincronización.</span><span class="sxs-lookup"><span data-stu-id="b7c45-280">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="b7c45-281">El contexto de sincronización de Blazor Server intenta emular un entorno de un solo subproceso para que coincida estrechamente con el modelo WebAssembly en el explorador, que es de un solo subproceso.</span><span class="sxs-lookup"><span data-stu-id="b7c45-281">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="b7c45-282">En todo momento, el trabajo se realiza en exactamente un subproceso, lo que da la impresión de un único subproceso lógico.</span><span class="sxs-lookup"><span data-stu-id="b7c45-282">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="b7c45-283">Nunca se ejecutan dos operaciones simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-283">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="b7c45-284">Evitar las llamadas de bloqueo de subprocesos</span><span class="sxs-lookup"><span data-stu-id="b7c45-284">Avoid thread-blocking calls</span></span>

<span data-ttu-id="b7c45-285">Por lo general, no llame a los métodos siguientes.</span><span class="sxs-lookup"><span data-stu-id="b7c45-285">Generally, don't call the following methods.</span></span> <span data-ttu-id="b7c45-286">Los métodos siguientes bloquean el subproceso y, por tanto, impiden que la aplicación reanude el trabajo hasta que se complete <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="b7c45-286">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="b7c45-287">Invocación de métodos de componentes externamente para actualizar el estado</span><span class="sxs-lookup"><span data-stu-id="b7c45-287">Invoke component methods externally to update state</span></span>

<span data-ttu-id="b7c45-288">En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que envía al contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-288">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="b7c45-289">Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:</span><span class="sxs-lookup"><span data-stu-id="b7c45-289">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="b7c45-290">Registre `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-290">Register the `NotifierService`:</span></span>

* <span data-ttu-id="b7c45-291">En Blazor WebAssembly, registre el servicio como singleton en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-291">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="b7c45-292">En Blazor Server, registre el servicio como con ámbito en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-292">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="b7c45-293">Use el elemento `NotifierService` para actualizar un componente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-293">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="b7c45-294">En el ejemplo anterior, `NotifierService` invoca el método `OnNotify` del componente fuera del contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-294">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="b7c45-295">`InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.</span><span class="sxs-lookup"><span data-stu-id="b7c45-295">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="b7c45-296">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-296">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="b7c45-297">Uso de \@key para controlar la conservación de elementos y componentes</span><span class="sxs-lookup"><span data-stu-id="b7c45-297">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="b7c45-298">Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-298">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="b7c45-299">Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.</span><span class="sxs-lookup"><span data-stu-id="b7c45-299">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="b7c45-300">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-300">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b7c45-301">El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas.</span><span class="sxs-lookup"><span data-stu-id="b7c45-301">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="b7c45-302">Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-302">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="b7c45-303">Esto puede hacer que se genere una nueva representación más compleja de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="b7c45-303">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="b7c45-304">En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.</span><span class="sxs-lookup"><span data-stu-id="b7c45-304">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="b7c45-305">El proceso de asignación se puede controlar con el atributo de directiva [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="b7c45-305">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="b7c45-306">[`@key`][5] hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="b7c45-306">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b7c45-307">Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-307">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="b7c45-308">Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-308">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="b7c45-309">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="b7c45-309">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b7c45-310">Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-310">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="b7c45-311">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="b7c45-311">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b7c45-312">Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-312">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="b7c45-313">En algunos escenarios, el uso de [`@key`][5] reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.</span><span class="sxs-lookup"><span data-stu-id="b7c45-313">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7c45-314">Las claves son locales de cada componente o elemento contenedor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-314">Keys are local to each container element or component.</span></span> <span data-ttu-id="b7c45-315">Las claves no se comparan globalmente en todo el documento.</span><span class="sxs-lookup"><span data-stu-id="b7c45-315">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="b7c45-316">Cuándo usar \@key</span><span class="sxs-lookup"><span data-stu-id="b7c45-316">When to use \@key</span></span>

<span data-ttu-id="b7c45-317">Normalmente, usar [`@key`][5] tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) y haya un valor adecuado para definir el elemento [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="b7c45-317">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="b7c45-318">También se puede usar [`@key`][5] para impedir que Blazor conserve un subárbol de componentes o elementos cuando un objeto cambie:</span><span class="sxs-lookup"><span data-stu-id="b7c45-318">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="b7c45-319">Si `@currentPerson` cambia, la directiva de atributo [`@key`][5] fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="b7c45-319">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="b7c45-320">Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.</span><span class="sxs-lookup"><span data-stu-id="b7c45-320">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="b7c45-321">Cuándo no usar \@key</span><span class="sxs-lookup"><span data-stu-id="b7c45-321">When not to use \@key</span></span>

<span data-ttu-id="b7c45-322">Las comparaciones con [`@key`][5] repercuten en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b7c45-322">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="b7c45-323">El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar [`@key`][5] únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7c45-323">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="b7c45-324">Aun cuando [`@key`][5] no se use, Blazor conserva las instancias de componentes y elementos secundarios lo máximo posible.</span><span class="sxs-lookup"><span data-stu-id="b7c45-324">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="b7c45-325">La única ventaja de utilizar [`@key`][5] es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.</span><span class="sxs-lookup"><span data-stu-id="b7c45-325">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="b7c45-326">Qué valores usar en \@key</span><span class="sxs-lookup"><span data-stu-id="b7c45-326">What values to use for \@key</span></span>

<span data-ttu-id="b7c45-327">Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en [`@key`][5]:</span><span class="sxs-lookup"><span data-stu-id="b7c45-327">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="b7c45-328">Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="b7c45-328">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="b7c45-329">Esto garantiza la conservación en función de la igualdad de las referencias de objetos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-329">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="b7c45-330">Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-330">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="b7c45-331">Asegúrese de que los valores usados en [`@key`][5] no entran en conflicto.</span><span class="sxs-lookup"><span data-stu-id="b7c45-331">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="b7c45-332">Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes.</span><span class="sxs-lookup"><span data-stu-id="b7c45-332">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="b7c45-333">Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="b7c45-333">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="b7c45-334">Parámetros sobrescritos</span><span class="sxs-lookup"><span data-stu-id="b7c45-334">Overwritten parameters</span></span>

<span data-ttu-id="b7c45-335">El marco Blazor impone con carácter general una asignación de parámetro de componentes primarios a secundarios segura:</span><span class="sxs-lookup"><span data-stu-id="b7c45-335">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="b7c45-336">Los parámetros no se sobrescriben de forma inesperada.</span><span class="sxs-lookup"><span data-stu-id="b7c45-336">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="b7c45-337">Los efectos secundarios se minimizan.</span><span class="sxs-lookup"><span data-stu-id="b7c45-337">Side-effects are minimized.</span></span> <span data-ttu-id="b7c45-338">Por ejemplo,se evitan representaciones adicionales, ya que pueden crear bucles de representación infinitos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-338">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="b7c45-339">Un componente secundario recibe nuevos valores de parámetro que posiblemente sobrescriban los valores existentes cuando el componente primario vuelva a representarse.</span><span class="sxs-lookup"><span data-stu-id="b7c45-339">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="b7c45-340">Sobrescribir valores de parámetro en un componente secundario de forma accidental suele producirse al desarrollar el componente con uno o varios parámetros enlazados a datos y cuando el desarrollador escribe directamente en un parámetro del elemento secundario:</span><span class="sxs-lookup"><span data-stu-id="b7c45-340">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="b7c45-341">El componente secundario se representa con uno o varios valores de parámetro del componente primario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-341">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="b7c45-342">El elemento secundario escribe directamente en el valor de un parámetro.</span><span class="sxs-lookup"><span data-stu-id="b7c45-342">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="b7c45-343">El componente primario vuelve a representar el valor del parámetro del elemento secundario y lo sobrescribe.</span><span class="sxs-lookup"><span data-stu-id="b7c45-343">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="b7c45-344">La posibilidad de sobrescribir valores de parámetro se extiende también a los establecedores de propiedades del componente secundario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-344">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="b7c45-345">**Nuestra orientación general es no crear componentes que se escriban directamente en sus propios parámetros.**</span><span class="sxs-lookup"><span data-stu-id="b7c45-345">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="b7c45-346">Considere el componente `Expander` erróneo siguiente que:</span><span class="sxs-lookup"><span data-stu-id="b7c45-346">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="b7c45-347">Representa el contenido secundario.</span><span class="sxs-lookup"><span data-stu-id="b7c45-347">Renders child content.</span></span>
* <span data-ttu-id="b7c45-348">Alterna la visualización del contenido secundario con un parámetro de componente (`Expanded`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-348">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="b7c45-349">El componente escribe directamente en el parámetro `Expanded`, que muestra el problema con los parámetros sobrescritos y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="b7c45-349">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="b7c45-350">El componente `Expander` se agrega a un componente principal que podría llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="b7c45-350">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="b7c45-351">Al principio, los componentes `Expander` se comportan de manera independiente cuando se alternan sus propiedades de `Expanded`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-351">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="b7c45-352">Los componentes secundarios mantienen sus estados según lo previsto.</span><span class="sxs-lookup"><span data-stu-id="b7c45-352">The child components maintain their states as expected.</span></span> <span data-ttu-id="b7c45-353">Cuando se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en el componente principal, el parámetro `Expanded` del primer componente secundario se restablece nuevamente en su valor inicial (`true`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-353">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="b7c45-354">No se restablece el valor `Expanded` del segundo componente de `Expander`, porque no se representa ningún contenido secundario en el segundo componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-354">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="b7c45-355">Para mantener el estado en el escenario anterior, use un *campo privado* en el componente `Expander` para mantener su estado de alternancia.</span><span class="sxs-lookup"><span data-stu-id="b7c45-355">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="b7c45-356">El siguiente componente `Expander` revisado:</span><span class="sxs-lookup"><span data-stu-id="b7c45-356">The following revised `Expander` component:</span></span>

* <span data-ttu-id="b7c45-357">Acepta el valor del parámetro de componente `Expanded` del componente principal.</span><span class="sxs-lookup"><span data-stu-id="b7c45-357">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="b7c45-358">Asigna el valor del parámetro de componente a un *campo privado* (`expanded`) en el [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="b7c45-358">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="b7c45-359">Usa el campo privado para mantener su estado de alternancia interno, que muestra cómo evitar escribir directamente en un parámetro.</span><span class="sxs-lookup"><span data-stu-id="b7c45-359">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="b7c45-360">Para obtener información adicional, consulte [Error de enlace bidireccional de Blazor (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="b7c45-360">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="b7c45-361">Aplicación de un atributo</span><span class="sxs-lookup"><span data-stu-id="b7c45-361">Apply an attribute</span></span>

<span data-ttu-id="b7c45-362">En los componentes de Razor se pueden aplicar atributos con la directiva [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="b7c45-362">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="b7c45-363">En el siguiente ejemplo se aplica el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a la clase del componente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-363">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="b7c45-364">Atributos de elementos HTML condicionales</span><span class="sxs-lookup"><span data-stu-id="b7c45-364">Conditional HTML element attributes</span></span>

<span data-ttu-id="b7c45-365">Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET.</span><span class="sxs-lookup"><span data-stu-id="b7c45-365">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="b7c45-366">Si el valor es `false` o `null`, el atributo no se representa.</span><span class="sxs-lookup"><span data-stu-id="b7c45-366">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="b7c45-367">Si el valor es `true`, el atributo se representa minimizado.</span><span class="sxs-lookup"><span data-stu-id="b7c45-367">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="b7c45-368">En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:</span><span class="sxs-lookup"><span data-stu-id="b7c45-368">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="b7c45-369">Si `IsCompleted` es `true`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="b7c45-369">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="b7c45-370">Si `IsCompleted` es `false`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="b7c45-370">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="b7c45-371">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b7c45-371">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="b7c45-372">Algunos atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-372">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="b7c45-373">En esos casos, use un tipo `string` en lugar de `bool`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-373">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="b7c45-374">HTML sin formato</span><span class="sxs-lookup"><span data-stu-id="b7c45-374">Raw HTML</span></span>

<span data-ttu-id="b7c45-375">Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal.</span><span class="sxs-lookup"><span data-stu-id="b7c45-375">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="b7c45-376">Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-376">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="b7c45-377">El valor se analiza como HTML o SVG y se inserta en el DOM.</span><span class="sxs-lookup"><span data-stu-id="b7c45-377">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="b7c45-378">La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="b7c45-378">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="b7c45-379">En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-379">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="b7c45-380">Plantillas de Razor</span><span class="sxs-lookup"><span data-stu-id="b7c45-380">Razor templates</span></span>

<span data-ttu-id="b7c45-381">Los fragmentos de representación se pueden definir mediante la sintaxis de plantilla de Razor.</span><span class="sxs-lookup"><span data-stu-id="b7c45-381">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="b7c45-382">Las plantillas de Razor son una forma de definir un fragmento de interfaz de usuario y asumen el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="b7c45-382">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="b7c45-383">En el siguiente ejemplo se muestra cómo especificar los valores <xref:Microsoft.AspNetCore.Components.RenderFragment> y <xref:Microsoft.AspNetCore.Components.RenderFragment%601> y las plantillas de representación directamente en un componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-383">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="b7c45-384">Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="b7c45-384">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="b7c45-385">Salida representada del código anterior:</span><span class="sxs-lookup"><span data-stu-id="b7c45-385">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="b7c45-386">Recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="b7c45-386">Static assets</span></span>

<span data-ttu-id="b7c45-387">Blazor sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [`web root (wwwroot)` del proyecto](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="b7c45-387">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="b7c45-388">Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático.</span><span class="sxs-lookup"><span data-stu-id="b7c45-388">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="b7c45-389">En el ejemplo siguiente, `logo.png` se encuentra físicamente en la carpeta `{PROJECT ROOT}/wwwroot/images`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-389">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="b7c45-390">Los componentes de Razor **no** admiten la notación de virgulilla-barra diagonal (`~/`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-390">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="b7c45-391">Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b7c45-391">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="b7c45-392">Las aplicaciones auxiliares de etiquetas no se admiten en los componentes</span><span class="sxs-lookup"><span data-stu-id="b7c45-392">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="b7c45-393">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) no se admiten en los componentes de Razor (archivos `.razor`).</span><span class="sxs-lookup"><span data-stu-id="b7c45-393">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="b7c45-394">Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.</span><span class="sxs-lookup"><span data-stu-id="b7c45-394">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="b7c45-395">Imágenes con formato Scalable Vector Graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="b7c45-395">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="b7c45-396">Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) (`.svg`) mediante la etiqueta `<img>`:</span><span class="sxs-lookup"><span data-stu-id="b7c45-396">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="b7c45-397">Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos (`.css`):</span><span class="sxs-lookup"><span data-stu-id="b7c45-397">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="b7c45-398">Pero no todos los escenarios admiten el uso de marcado SVG en línea.</span><span class="sxs-lookup"><span data-stu-id="b7c45-398">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="b7c45-399">Si se coloca una etiqueta `<svg>` directamente en un archivo de componente (`.razor`), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-399">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="b7c45-400">Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y [`@bind`][10] no se puede usar con algunas etiquetas SVG.</span><span class="sxs-lookup"><span data-stu-id="b7c45-400">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="b7c45-401">Para más información, consulte la [compatibilidad con SVG en Blazor(dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="b7c45-401">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="b7c45-402">Comportamiento de la representación de espacios en blanco</span><span class="sxs-lookup"><span data-stu-id="b7c45-402">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7c45-403">A menos que se use la directiva [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) con un valor de `true`, el espacio en blanco adicional se quita de forma predeterminada si:</span><span class="sxs-lookup"><span data-stu-id="b7c45-403">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="b7c45-404">Está delante o detrás de un elemento.</span><span class="sxs-lookup"><span data-stu-id="b7c45-404">Leading or trailing within an element.</span></span>
* <span data-ttu-id="b7c45-405">Está delante o detrás de un parámetro `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-405">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="b7c45-406">Por ejemplo, el contenido secundario se pasa a otro componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-406">For example, child content passed to another component.</span></span>
* <span data-ttu-id="b7c45-407">Precede o sigue a un bloque de código de C#, como `@if` o `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-407">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="b7c45-408">La eliminación de espacios en blanco puede afectar a la salida representada cuando se usa una regla de CSS, como `white-space: pre`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-408">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="b7c45-409">Para deshabilitar esta optimización de rendimiento y conservar el espacio en blanco, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="b7c45-409">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="b7c45-410">Agregue la directiva `@preservewhitespace true` en la parte superior del archivo `.razor` para aplicar las preferencias a un componente específico.</span><span class="sxs-lookup"><span data-stu-id="b7c45-410">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="b7c45-411">Agregue la directiva `@preservewhitespace true` dentro de un archivo `_Imports.razor` para aplicar la preferencia a un subdirectorio completo o a todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7c45-411">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="b7c45-412">En la mayoría de los casos, no se requiere ninguna acción, ya que las aplicaciones normalmente seguirán funcionando con normalidad (pero más rápido).</span><span class="sxs-lookup"><span data-stu-id="b7c45-412">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="b7c45-413">Si la eliminación de espacios en blanco provoca problemas en un componente determinado, use `@preservewhitespace true` de ese componente para deshabilitar esta optimización.</span><span class="sxs-lookup"><span data-stu-id="b7c45-413">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b7c45-414">El espacio en blanco se conserva en el código fuente de un componente.</span><span class="sxs-lookup"><span data-stu-id="b7c45-414">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="b7c45-415">El texto de solo espacio en blanco se representa en el modelo Document Object Model (DOM) del explorador aunque no haya ningún efecto visual.</span><span class="sxs-lookup"><span data-stu-id="b7c45-415">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="b7c45-416">Considere el código de componente Razor siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7c45-416">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="b7c45-417">En el ejemplo anterior se representa el siguiente espacio en blanco innecesario:</span><span class="sxs-lookup"><span data-stu-id="b7c45-417">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="b7c45-418">Fuera del bloque de código `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-418">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="b7c45-419">Alrededor del elemento `<li>`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-419">Around the `<li>` element.</span></span>
* <span data-ttu-id="b7c45-420">Alrededor de la salida de `@item.Text`.</span><span class="sxs-lookup"><span data-stu-id="b7c45-420">Around the `@item.Text` output.</span></span>

<span data-ttu-id="b7c45-421">Una lista que contiene 100 elementos da como resultado 402 áreas de espacio en blanco, y ninguno de los espacios en blanco adicionales afecta visualmente a la salida representada.</span><span class="sxs-lookup"><span data-stu-id="b7c45-421">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="b7c45-422">Al representar HTML estático para componentes, no se conservaba el espacio en blanco dentro de una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="b7c45-422">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="b7c45-423">Por ejemplo, vea el origen del siguiente componente en la salida representada:</span><span class="sxs-lookup"><span data-stu-id="b7c45-423">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="b7c45-424">No se conserva el espacio en blanco del marcado de Razor anterior:</span><span class="sxs-lookup"><span data-stu-id="b7c45-424">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b7c45-425">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b7c45-425">Additional resources</span></span>

* <span data-ttu-id="b7c45-426"><xref:blazor/security/server/threat-mitigation>: incluye instrucciones sobre cómo crear aplicaciones de Blazor Server que deben afrontar situaciones de agotamiento de recursos.</span><span class="sxs-lookup"><span data-stu-id="b7c45-426"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#code)
[2]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#using)
[3]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#attributes)
[4]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#ref)
[5]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#key)
[6]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#inherits)
[7]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#attribute)
[8]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#namespace)
[9]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#page)
[10]: [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor#bind)
